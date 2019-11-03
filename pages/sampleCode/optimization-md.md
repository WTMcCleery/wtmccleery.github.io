<html>
	<head>
		<h1>W. Tyler McCleery, Ph.D.</h1>
		<!-- link to main stylesheet -->
		<link rel="stylesheet" type="text/css" href="/css/main.css">
		<!-- Rescale page to fit any device -->
		<meta name="viewport" content="width=device-width, initial-scale=1">
	</head>
	<body>
		<nav>
    		<ul>
        		<li><a href="/">Home</a></li>
        		<li><a href="/pages/research/research.html">Research</a></li>
	        	<!-- <li><a href="/pages/about/about.html">About</a></li> -->
        		<li><a href="/pages/cv/McCleery-Resume.pdf">Resume</a></li>
        		<li><a href="/pages/contact/contact.html">Contact Me</a></li>
    		</ul>
		</nav>
 </html>
	
# Optimization
            
The following code is written in Mathematica, v. 12.0

## Objective
Create a mesh of points that are spread according to an energy minimization function. Points that are closer to each other will have a higher energy cost. This will encourage points to spread with Monte Carlo-like updating.

### Generate random 2D spread of points

```
nCells = 20;
verbose = True;
boxSize = 20;
pts = RandomReal[{-boxSize/2, boxSize/2}, {nCells, 2}];
ptsNew = pts;
```
### Energy parameters
E = k/r^2
where E = energy, k = repulsion constant, and r = distance between 2 points

```
k = 1;
rArray = {};
totalEnergy = 100;
percentDisp = 0.05;
stepDisp = percentDisp*boxSize/2;
```
### Loop to minimize energy

```
j = 0;
While[(totalEnergy >= 1 && j < 10),
 (totalEnergy = 0;
  Do[
```
   Define Euclidean Distance between each set of 2 points
```
   rArray = 
    Table[EuclideanDistance[pts[[i]], pts[[j]]], {i, 1, 
       Length[pts]}, {j, 1, Length[pts]}] // MatrixForm;
```
Move point i by random displacement within percent displacement (e.g. 5% of box size 20 = \[PlusMinus]0.5 step size)
```
   ptsNew[[i]] = {pts[[i, 1]] + RandomReal[{-stepDisp, stepDisp}], 
     pts[[i, 2]] + RandomReal[{-stepDisp, stepDisp}]};
   rArrayNew = 
    Table[EuclideanDistance[ptsNew[[i]], ptsNew[[j]]], {i, 1, 
       Length[ptsNew]}, {j, 1, Length[ptsNew]}] // MatrixForm;
```
   Create energy list
```
   uArray = rArray;
   uArrayNew = rArrayNew;
```
Each point will have E = k/r energy with other points, and E = 0 energy with self
```
   Do[
    Do[
     If[p == q,
      uArray[[1, p, q]] = 0;
      uArrayNew[[1, p, q]] = 0,
      uArray[[1, p, q]] = k/(rArray[[1, p, q]])^2;
      uArrayNew[[1, p, q]] = k/(rArrayNew[[1, p, q]])^2;
      ],
     {q, 1, Length[rArray[[1, 1]]]}],
    {p, 1, Length[rArray[[1, 1]]]}];
```
Sum total energy
```
   EnergyOrig = Total[uArray[[1]], 1];
   EnergyNew = Total[uArrayNew[[1]], 1];
```
Conditions to keep random displacement of point i:
   1. points must stay within boundary,
   2. Energy will reduce, or accept increase in energy with 10% probability
   Note: probability condition prevents annealing to local but not global energy minimum
```
   If[
    ((ptsNew[[i, 1]] <= 
        10) && (ptsNew[[i, 1]] >= -10) && (ptsNew[[i, 2]] <= 
        10) && (ptsNew[[i, 2]] >= -10)
      && ((EnergyNew[[i]] < EnergyOrig[[i]]) || (RandomReal[] > 0.9))),
    pts[[i]] = ptsNew[[i]];
    totalEnergy += EnergyNew[[i]],
    totalEnergy += EnergyOrig[[i]]
    ],
   {i, Length[pts]}];
  If[verbose, Print[totalEnergy]];
  j += 1;
  )
 ]
```
Display Output every 10 iterations
