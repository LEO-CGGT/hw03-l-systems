# Christmas Tree
<img alt="final render" src="img/render.png">   
<img alt="final gif" src="img/gif.png">    
Rule:

```
g(0)F(0.6)AM
A=F(0.06 + rand(i)*0.01)DDDDDDDDDDDDA
B=g(1)!(0.4)^(rand(i) * 10 + 35)F^(rand(i) * 10 + 35)C  
C=F(0.04 + rand(i)*0.02)EEEOC 
D=/(rand(i) * 10 + 25)[^B]
E=g(2)/(rand(i) * 10 + 55)[+F]
I=J:0.00001
N=K:0.000005 
O=IN:0.2
```

# Homework 4: L-systems
Houdini L-Systems Option
* Design a plant. Use at least 6 grammar rules (arbitrary number chosen for complexity)
* The plant should have flowers, fruits, leaves or something that is not just branches
* The plant must be rendered nicely, no wireframe or naked gray lambert

# Design Write-up
## Step 1 - Breaking down the components of a Christmas tree
The first step is break down what are the components that form a Christmas tree.    
Just like most other trees, it has a stump, branches, and leaves. There are also decoractions on the tree.    
We divide it into different components and conquer them seperately. 
## Step 2 - Generating leaves
The leaves of the Christmas tree is a trident-look shape. So, by having three turtle branches, and rotate each of them by 60 degrees, we are able to generate this shape. By recurring itself at the end, we have the rules for the leaves.    
`FA`, `A=F/(60)[+F]/(60)[+F]/(60)[+F]A`   
<img alt="leave1" src="img/1.png">   
<img alt="leave2" src="img/3.png">   
## Step 3 - Generating the stump and branches
The stump is generally easy to generate. At each iteration, we simply keeps moving forward, that will generate a straight line as the stump.   
At the same time, we want each segment of the stump to have a circle of branches. This can be achieved by having n turtle branches, and each rotate 360/n degrees.   
By combining both ideas above, we have a stump and branches.   
`FA`, `A=F[^B]/(60)[^B]/(60)[^B]/(60)[^B]/(60)[^B]/(60)[^B]/(60)A`, `B=F`   
<img alt="stump-branches" src="img/5-iter6.png">    
## Step 4 - Combining leaves, branches, and the stump
By combining the above rules, we have a tree!    
<img alt="combining rules" src="img/6-iter5.png">    
However, comparing the result with an actual Christmas tree, we notice that the branches should be facing downwards instead of upwards.   
By chaging the rotation degrees of the branches, we have a better result:   
<img alt="growing down" src="img/7-growing-down-iter5.png">    
## Step 5 - Tuning attibutes and adding randomness
Now, we can slightly change the rotation degrees, adding the number of branches, and increase the number of iterations. The result starts to look like a real Christmas tree!   
<img alt="tree-skeleton-done" src="img/9.png">    
The problem with the tree right now is that it looks too uniform. We can fix it by adding randomness to the rotation degrees. Instead of always rotating a fixed degree, now we give each branch and leave the freedom to rotate in a range. The result looks much more natural.   
<img alt="add-randomness" src="img/11.png">    
## Step 6 - Adding decorations and colors
The next step is to add decorations and colors to the tree. Adding decorations is simple, we can create a box and a sphere primitive, connect them as the input to the L-system, and then use a rule to add them randomly. There is usually a star at the top of the Christmas tree. To add it, I found a mesh online ([source](https://sketchfab.com/3d-models/christmas-star-b3b13e26164948c69910119558616485)), and imported it to Houdini. By adding it at the end of the rule that generate the stump, it is added to the top of the tree once.       
<img alt="add-decors" src="img/10.png">     
Adding colors is a little more complicated, since each part of the tree has different colors. Luckily, Houdini allows us to group parts generated by differnt rules. With the L-system groups, we can then use the `Split` node to divide them and shade them seperately. By creating materials for the decors, leaves, branches, and the stump, I'm finally able to shade the tree correctly.    
<img alt="add-colors" src="img/12.png">    
## Step 7 - Adjust the stump thickness
Comparing it with a real Christmas tree, the only problem now is the thickness of the stump. Instead of being thick on the bottom and thin on the top, it is  is uniform. To achieve this effect, I wrote a simple script that applies a scale to the stump according to its y value:   
```
vector max = getbbox_max(0);
float scale = 3 * (max[1] - @P.y);
@P.x *= scale;
@P.z *= scale;
```
This scales down the thickness of the stump as it goes up.    
<img alt="stump-thickness" src="img/14.png">    
## Step 8 - Setting up for rendering
