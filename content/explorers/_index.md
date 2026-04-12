---
title: "R and Stats Explorers"
description: "Interactive tools for learning R and statistics"
type: page
layout: single
---

Below is a collection of the interactive tools I've built for my stats students (which I call "Explorers"). The goal is to make coding and stats more accessible and help students more clearly see the connection between what they want the code to do (e.g., "make the points in my ggplot different colors for different groups" or "take my dataset, grab only the first three columns, and filter out the rows in which some criterion is met") and how that's reflected in the code.


In most cases, the Explorers consist of three panels: The left one has the controllers, the middle one displays the data or plot, and the right one displays the code (often with comments to help the user understand the function). Short descriptions will pop up if you hover over portions of the code, and detailed descriptions can be found below the code chunks. Be sure to read the welcome message below the code on the right for instructions about how to use each tool! 


Good luck, have fun, and remember: WE ARE ALL MATH PEOPLE.

<div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(220px,1fr));gap:14px;margin-top:1.5rem;">

   <a href="/explorers/ggplot-visualizer/" target="_blank" style="display:block;text-decoration:none;background:#fdfcf9;border:1.5px solid #dbd3c3;border-radius:10px;padding:16px 18px;transition:border-color .15s,box-shadow .15s;" onmouseover="this.style.borderColor='#b85520';this.style.boxShadow='0 2px 8px rgba(184,85,32,0.12)'" onmouseout="this.style.borderColor='#dbd3c3';this.style.boxShadow='none'">
    <div style="font-family:'Lora',Georgia,serif;font-weight:600;color:#1c1811;margin-bottom:5px;">ggplot Visualizer</div>
    <div style="font-size:0.82rem;color:#79705f;line-height:1.4;">Build layered plots by tweaking aesthetics and geoms</div>
  </a> 
  
  <a href="/explorers/dplyr-explorer/" target="_blank" style="display:block;text-decoration:none;background:#fdfcf9;border:1.5px solid #dbd3c3;border-radius:10px;padding:16px 18px;transition:border-color .15s,box-shadow .15s;" onmouseover="this.style.borderColor='#b85520';this.style.boxShadow='0 2px 8px rgba(184,85,32,0.12)'" onmouseout="this.style.borderColor='#dbd3c3';this.style.boxShadow='none'">
    <div style="font-family:'Lora',Georgia,serif;font-weight:600;color:#1c1811;margin-bottom:5px;">dplyr Explorer</div>
    <div style="font-size:0.82rem;color:#79705f;line-height:1.4;">Filter, select, mutate, and arrange data using dplyr verbs</div>
  </a>

   <a href="/explorers/pivot-explorer/" target="_blank" style="display:block;text-decoration:none;background:#fdfcf9;border:1.5px solid #dbd3c3;border-radius:10px;padding:16px 18px;transition:border-color .15s,box-shadow .15s;" onmouseover="this.style.borderColor='#b85520';this.style.boxShadow='0 2px 8px rgba(184,85,32,0.12)'" onmouseout="this.style.borderColor='#dbd3c3';this.style.boxShadow='none'">
    <div style="font-family:'Lora',Georgia,serif;font-weight:600;color:#1c1811;margin-bottom:5px;">Pivot Explorer</div>
    <div style="font-size:0.82rem;color:#79705f;line-height:1.4;">Reshape data between wide and long formats with tidyr</div>
  </a>

  <a href="/explorers/indexing-explorer/" target="_blank" style="display:block;text-decoration:none;background:#fdfcf9;border:1.5px solid #dbd3c3;border-radius:10px;padding:16px 18px;transition:border-color .15s,box-shadow .15s;" onmouseover="this.style.borderColor='#b85520';this.style.boxShadow='0 2px 8px rgba(184,85,32,0.12)'" onmouseout="this.style.borderColor='#dbd3c3';this.style.boxShadow='none'">
    <div style="font-family:'Lora',Georgia,serif;font-weight:600;color:#1c1811;margin-bottom:5px;">Indexing Explorer</div>
    <div style="font-size:0.82rem;color:#79705f;line-height:1.4;">Learn how R indexes vectors and data frames, and practice using logical operators and Booleans to filter rows </div>
  </a>

   <a href="/explorers/file-path-explorer/" target="_blank" style="display:block;text-decoration:none;background:#fdfcf9;border:1.5px solid #dbd3c3;border-radius:10px;padding:16px 18px;transition:border-color .15s,box-shadow .15s;" onmouseover="this.style.borderColor='#b85520';this.style.boxShadow='0 2px 8px rgba(184,85,32,0.12)'" onmouseout="this.style.borderColor='#dbd3c3';this.style.boxShadow='none'">
    <div style="font-family:'Lora',Georgia,serif;font-weight:600;color:#1c1811;margin-bottom:5px;">File Path Explorer</div>
    <div style="font-size:0.82rem;color:#79705f;line-height:1.4;">Practice navigating file paths to  build intuition about working directories and reading data into R</div>
  </a>   

  <a href="/explorers/distribution-explorer/" target="_blank" style="display:block;text-decoration:none;background:#fdfcf9;border:1.5px solid #dbd3c3;border-radius:10px;padding:16px 18px;transition:border-color .15s,box-shadow .15s;" onmouseover="this.style.borderColor='#b85520';this.style.boxShadow='0 2px 8px rgba(184,85,32,0.12)'" onmouseout="this.style.borderColor='#dbd3c3';this.style.boxShadow='none'">
    <div style="font-family:'Lora',Georgia,serif;font-weight:600;color:#1c1811;margin-bottom:5px;">Distribution Explorer</div>
    <div style="font-size:0.82rem;color:#79705f;line-height:1.4;">Adjust the parameters of probability distributions to visualize area under the curve and learn about the p*() family of functions</div>
  </a>

  <a href="/explorers/measurement-explorer/" target="_blank" style="display:block;text-decoration:none;background:#fdfcf9;border:1.5px solid #dbd3c3;border-radius:10px;padding:16px 18px;transition:border-color .15s,box-shadow .15s;" onmouseover="this.style.borderColor='#b85520';this.style.boxShadow='0 2px 8px rgba(184,85,32,0.12)'" onmouseout="this.style.borderColor='#dbd3c3';this.style.boxShadow='none'">
    <div style="font-family:'Lora',Georgia,serif;font-weight:600;color:#1c1811;margin-bottom:5px;">Reliability & Validity Explorer</div>
    <div style="font-size:0.82rem;color:#79705f;line-height:1.4;">Explore how reliability and validity relate to each other and to the quality of measurement</div>
  </a>

</div>
