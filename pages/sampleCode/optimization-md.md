<html>
	<head>
		<title>W. Tyler McCleery, Ph.D.</title>
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
