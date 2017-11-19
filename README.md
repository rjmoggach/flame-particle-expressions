# Flame Particle Expressions

This is my collection of flame particle expressions. I generally clone it to my flame user folder and update as I make changes.

## SPEC.. Sources, Particles, Et Cetera

The commonly referenced list is below:

### Transparency Functions

#### simple-fade-off (evenly from start)

    transparency = lifetimeI

#### simple-fade-up (evenly from start)

    transparency = (1-lifetimeI)

#### approx-fade-off

    transparency = sin(lifetimeI)

#### normal-fade-off (over life)

    transparency =sin(lifetimeI*1.5)

#### normal-fade-up

    transparency = cos(lifetimeI*1.5)

#### fade-in-out (over life)

    transparency = sin(lifetimeI*3.14)

#### fade-in-out+cnt

    transparency = power * ( sin( lifetimeI * 3.14 ) )

This is better than `transparency = lifetimeI` because with the previous formula you would have particles 'pop' on and then fade. I had some popping occur when I was trying to make smoke from a chimney, but it gave this interesting puffing effect.

#### fade-midway+cnt

    transparency = transparency / sin(lifetimeI) * power

fade down in the middle of the particles life, with power control (power is between 0 and 1) (control with power)

#### fade-up-off-fast

    transparency = -0.05(pow(tan((lifetimeI-0.5)/0.37, 2)) +1

Produces a very steep up flat top , very steep down curve, no explict control.

#### fade-up-out+cnt2 (use magnitude>1)

    transparency = -(magnitude*lifetimeI) (lifetimeI - 1)

#### trans-near-a-point

    transparency = 1 - smoothstep(100,400, length(pos-(100,0,500))))

In this case only near coordinates 100,0,500 - the old light default location, 100 and 400 define the distances close and far - that limit the transparency. I used this when I wanted rain to light up around a street light, - unlike a matte it works in 3 space, like a ball - when the rain enters the ball it lights up- very nice

#### fade-off+cnt

    transparency=pow(lifetimeI,magnitude)

#### fade-up-off-random

    transparency = sin(lifetimeI + turbulence(pos,1))

A great function that acts more naturally in fading off particles

#### linear-fade-up-off+cnt

    transparency = ( ((1-lifetimeI)<power)*smoothstep(0,1,(1-lifetimeI)/power)) + (((1-lifetimeI)>=power)*smoothstep(0,1,lifetimeI/(1-damping)) )

This amazing function allows you to move the mid point of the fadeup/off and effect the out (via damping) try `damping = 25%` and `power =0.5`

#### linear-fade-up-off+cnt2

    transparency = (((1-lifetimeI)<power)*(1-lifetimeI)/power) + (((1-lifetimeI)>=power)*lifetimeI/(1-damping))

`fade up to frame` set by `power` -`fade down from frame` set by `damping` (don\'t use damping values lower than power) -change damping to power if you want it to fade up and down immediately. Try `power=0.25`, `damping=75`

#### hold-then-off

    transparency = (lifetimeI>0.5)*lifetimeI + (lifetimeI<=0.5)*0.5

`(lifetimeI>0.5)` returns 1 when `lifetimeI` is greater than 0.5 and zero when `lifetimeI` is less than 0.5
and likewise `(lifetimeI<=0.5)` returns 1 when `lifetimeI` is less than or equal to 0.5 and zero when `lifetimeI` is greater than 0.5 thus multiplying the `then` and `else` functions by their respective conditional expressions and adding them together results in transparency fading down to 0.5 until halfway through the particles life and then remaining at 0.5 till the end....


### Colour functions

#### yellow-green-blue

    rgb = (red*lifetimeI, sin(lifetimeI), 25)

Colors shift through the range during their life

#### fire-yellow-red-black

    rgb=(red*lifetimeI, green*pow(lifetimeI,2), 0)

In Surface/Geometry, keep the diffuse/specular/ambient colours as default (white/white/black). The first frame of the particles will be white, but then jumps to yellow because of the above expression. The green channel drops faster than the red channel, producing orange before red and then black. (tip: great for fire)

#### fire-yellow-red-black+cntl

    rgb=(red*lifetimeI, green*pow(lifetimeI,2),blue*pow(lifetimeI,magnitude))

If you want white to cycle to yellow, you could add an expression in the blue channel that drops very quickly . try magnitude =10

#### red-purple

    rgb = ((1,0,0)-(0,0,1))*smoothstep(0,1,lifetimeI))+(0,0,1)

This will make the color go from red-purple,


### Turbulence functions

#### speed-noise+cnt1

    speed = speed + cross ((power, power, power), noise3(pos))

Changing the power and speed affect the turbulence...try power=3.0

#### speed-noise+cnt2

    speed = speed + cross ((power, power, power), noise3(pos)) * magnitude

Changing the power, speed and magnitude all affect the turbulence... * note:In the above two examples, you can achieve some very interesting effects by keyframing the power, speed or magnitude channels. For instance, start out with very little turbulence and then ramp up into something really chaotic!

#### speed-turbulence+cntl1

    speed = speed + cross( (power,power,power), turbulence3(pos,1)

Same as above but using turbulence rather than the more 'random' noise function. note it is turbulence3 as we need the vector version.

    speed=(speed+cross(power*((1-lifetimeI)/2), power *((1-lifetimeI)/2), power*((1-lifetimeI)/2)), turbulance3(pos,1)))

Turbulence 1: Speed is defined as speed = speed + another speed vector ,.. plus it scales down the final vector by lifetime.

    speed=(speed+cross((power*(1-(lifetimeI/2)), power*(1-(lifetimeI/2)), power*(1-(lifetimeI/2))), turbulence3(pos,1)))/ (magnitude,magnitude,magnitude)

Turbulence 2: As the speed is defined as speed = speed + another speed vector ,.. we also need to scale down the final vector. So you need to divide everything by say (magnitude,magnitude,magnitude)

#### small-turbulence

    speed=speed*0.95+turbulence3(pos*0.01,1)

When rendering out thisone with let\'s say a full light with 180 spread on it as the generator what you will see are the obvious flows and eddies that the particles move through (use lines to see this, only one manipulator for the function).

#### small-turbulence2

    speed=speed*0.95+turbulence3((pos+(0,0,frame*20))* 0.01,1)

In this formula you begin to see the flows and eddies and then they are suddenly disrupted. In the first formula the variables are 0.95 and 0.01. In the second the 20 is also a variable. A lower number should make the swirls evolve at a different rate. The 0.01 value in both lines effects the apparent `lattice` scale. A smaller number makes for a bigger lattice, creating bigger/looser swirls. The range for this will be 0.1-0.001.

#### small-turbulence3

    speed = speed + (sin(lifetimeI/2 + 3*turbulence(pos,1)), sin(lifetimeI/2 + 3*turbulence(pos,1)), sin(lifetimeI/2 + 3*turbulence(pos,1)) )
