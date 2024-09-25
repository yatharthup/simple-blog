> Clickjacking is an interface-based attack in which a user is tricked into clicking on actionable content on a hidden website by clicking on some other content in a decoy website

As an example, consider that the user would click upon a "Download" button that appears normal at the first glance. Viewing the HTL source code , however, reveals that there is an iFrame (in-line HTML frame) that covers the button and redirects the user to a different website (in essence a more advanced Phishing strat)

> This attack differs from a CSRF attack in that the user is required to perform an action such as a button click whereas a CSRF attack depends upon forging an entire request without the user's knowledge or input.

CSRF --> Cross Site Req Forgery


## How to construct a basic clickjacking attack

Clickjacking attacks use CSS to create and manipulate layers. The attacker incorporates the target website as an iframe layer overlaid on the decoy website. An example using the style tag and parameters is as follows:

<head> <style> #target_website { position:relative; width:128px; height:128px; opacity:0.00001; z-index:2; } #decoy_website { position:absolute; width:300px; height:400px; z-index:1; } </style> </head> ... <body> <div id="decoy_website"> ...decoy web content here... </div> <iframe id="target_website" src="https://vulnerable-website.com"> </iframe> </body>`

The clever part is that the Actual website is below in a frame while the clickjacking frame is above it, at minimal opacity.


### More on it
The target website iframe is positioned within the browser so that there is a precise overlap of the target action with the decoy website using appropriate width and height position values. Absolute and relative position values are used to ensure that the target website accurately overlaps the decoy regardless of screen size, browser type and platform. The z-index determines the stacking order of the iframe and website layers. The opacity value is defined as 0.0 (or close to 0.0) so that the iframe content is transparent to the user. Browser clickjacking protection might apply threshold-based iframe transparency detection (for example, Chrome version 76 includes this behavior but Firefox does not). The attacker selects opacity values so that the desired effect is achieved without triggering protection behaviors.

