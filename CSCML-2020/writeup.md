#Prosopagnosia  
> My doctor has diagnosed me with Prosopagnosia, could you maybe help? I can't distinguish between a face a flag...  
  
In this challenge I found an unintended solution by looking at the date of the picture, we can see that each image is created at June 7 03:14 or 03:13, by doing reverse regex we can see that there is one image that is created at 03:39, opening it give us a picture of a face but a tiny flag at the bottom.  
Flag : `cscml2020{ml_can_be_learned_easily_by_examples}`  
  
#JSGame  
> Can you play my JS game with smallest rounds possible?  
  
Because I don't really like to read code, I solved this challenge by trial and error.  
First of all, we need to know our win condition, or how to actually set all the number to zero without computer keeps increment it, using two player mode, we can see that computer cannot do anything to a number that reaches max, when all n numbers has reached max, then we can put zero to all the numbers without the computer incrementing anything. This is possible because when we put zero in a 2, the arrow shift to the next number, and it's computer's move, but because the next number is also 2, that means the computer can't do anything, and we can put zero again, and again, and again.  
  
  
Now that we knows how to set the numbers to all 0, we need to know how to set the number to all max, and to know how to set all number to max, we need to know how to set one of the numbers to max.  
at our very first move, we can't make progress by choosing do nothing, when we choose put zero, the computer put 1 to the number next to it, after that, we can keep doing nothing and the computer will fill all the numbers to 1.  
after this step, putting zero to one number lead us to previous step, putting zero to two number lead us to previous step, but putting zero to three number give us a 2 and a 1.  
  
  
After this we can see that the 1 on the left keeps filled with one if we put zero in it, and everytime we put zero, the number next to it increased, so we can keep put zero on 1 on the left until the upper numbers becomes 2.  
  
  
Now that we know how to set the number at the top to 2, we need to find out the way to set the number on the right to 2, this can be done by setting both number on the left and at the top to zero once the number at the top reaches 2, doing so will make the number on the right increased by 1
  
  
now we can repeat the step before and set the number on the right to 2, this time the number on the left will be filled by one, so make sure you set both numbers to 0.  
  

Now we can repeat 3 steps before and set the number at the top to 2, than keep do nothing until all the numbers become 2, and then set everything to 0.  
Unfortunately this will take 85 rounds :P, this can be done by setting the 2 AT THE BOTTOM instead of at the top into 0 at step three, because the position is symmetrical, we can save 2 move and the position will be upside down, we can save another 2 move by doing the same thing at step 4, because the position are symmetrical again, we can save 2 move by setting the 2's at the top instead of at the bottom, thus flipping the position back, and now we can solve that in total of 81 move and get the flag.  
Flag : `CSCML2020{.+.0+..+..+000++...0+..+...0+.0+.+...0+..+...0+..+.00+.+...0+..+...0+..+...+0000}`  
