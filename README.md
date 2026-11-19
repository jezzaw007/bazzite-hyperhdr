# bazzite-hyperhdr
HyperHDR working on Bazzite  as a single compose file

# Description
I was trying to get HyperHDR working on bazzite without layering it using rpm-ostree
I had read that the preferred choice fo rthis app was to use a distroboc container
After a little messing about using soem prebuilt HyperHDR images, I soon learned that they were all aimed primarily at USB 
attached devices only, and did not contain any pipewire library

That out the way, so I figured a ubuntu image with the necessary packages was the only way.
Being the lazy admin I am, I wanted a single compose to do the whole job 

Here we have a single compose file, pulls the ubutnu image and installs the correct libraries and settings. 


# Instructions
Replace 'user' in the user volume path with your bazzite username
