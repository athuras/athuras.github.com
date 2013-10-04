---
layout: post
title:  "Oh what happy day."
date:   2013-10-03 20:31:08
categories: jekyll update
---

This is an absolutely fantastic walkthrough:
http://web.cecs.pdx.edu/~mpj/pubs/composing-fractals.pdf

Here is the code:

{% highlight haskell %}

type Point          = (Float, Float)
type Image color    = Point -> color
type Grid a         = [[a]]


next :: Point -> Point -> Point
next (u, v) (x, y) = (x * x - y * y + u, 2 * x * y + v)

mandelbrot :: Point -> [Point]
mandelbrot p = iterate (next p) (0, 0)

--Approximation
fairlyClose :: Point -> Bool
fairlyClose (u, v) = (u * u + v * v) < 100

inMandelbrotSet :: Point -> Bool
inMandelbrotSet p = all fairlyClose (mandelbrot p)

approxTest :: Int -> Point -> Bool
approxTest n p = all fairlyClose (take n (mandelbrot p))

--Colors!
chooseColor :: [color] -> [Point] -> color
chooseColor palette = (palette !!).length.take n.takeWhile fairlyClose
                      where n = length palette - 1

fracImage :: (Point -> [Point]) -> [color] -> Image color
fracImage fractal palette = chooseColor palette.fractal

--Grids
grid :: Int -> Int -> Point -> Point -> Grid Point
grid c r (xs, ys) (xm, ym) = [[(x, y) | x <- for c xs xm] | y <- for r ys ym]

for :: Int -> Float -> Float -> [Float]
for n min max = take n [min, min + delta ..]
                where delta = (max - min) / fromIntegral (n - 1)

sample :: Grid Point -> Image color -> Grid color
sample points image = map (map image) points

draw :: Grid Point -> (Point -> [Point]) -> [color] -> (Grid color -> image) -> image
draw points fractal palette render = render (sample points (fracImage fractal palette))

--Palettes
charPalette :: [Char]
charPalette = "    .~:;o-!|?/<>X+={^O#%&@8*$"

charRender :: Grid Char -> IO()
charRender = putStr.unlines

figure1 = draw points mandelbrot charPalette charRender
          where points = grid 79 37 (-2.25, -1.5) (0.75, 1.5)

{% endhighlight %}

Figure 1:

{% highlight haskell %}
                            ............................................
                        ....................................................
                    ..........................................................
                 .............................~~~~~~~~~~~~~~~~.................
              ............................~~~~~~~~~~:::::~~~~~~~~..............
            .........................~~~~~~~~~~~~:::;-<o;;:::~~~~~~~...........
         ........................~~~~~~~~~~~~~:::::;o-|&|?$o;::~~~~~~~~........
       ......................~~~~~~~~~~~~~~::::::;;o-!?>^?!o;;::::~~~~~~~......
     ....................~~~~~~~~~~~~~~:::::::;;;o->=$$$$*X<-o;;::::~~~~~~~....
   ...................~~~~~~~~~~~~~~:::::;;;;oooo-!|%$$$$$$/!-oo;;;;:::~~~~~...
 ..................~~~~~~~~~~~~~:::::::;;o!$++||/$<$=#$$$$=X{/$|---!Oo;:~~~~~..
................~~~~~~~~~~::::::::::;;;;oo!/#$$O$$$$$$$$$$$$$$$$<$$$O|;::~~~~~.
..............~~~~~~~~:::::::;;;;;;;;;oo-|<<^$$$$$$$$$$$$$$$$$$$$$$&?-;;::~~~~~
...........~~~~~~~~~::::;?|ooooooooooo-!|X$$$$$$$$$$$$$$$$$$$$$$$$$$<!!o:::~~~~
.........~~~~~~~~:::::;;o!{/??|/%?||!!!?{$$$$$$$$$$$$$$$$$$$$$$$$$$$$$^o;::~~~~
.......~~~~~~~::::::;;;o--|X$$@$$$$$$</>$$$$$$$$$$$$$$$$$$$$$$$$$$$$$>*o;::~~~~
.....~~~~~~::::::;;;o-!!!?$$$$$$$$$$$$##$$$$$$$$$$$$$$$$$$$$$$$$$$$$$/-;:::~~~~
....~~~~:::;;o;oooo--!/O$^$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$>-o;:::~~~~
....~~:#$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$X|-o;;:::~~~~
....~~~~:::;;o;oooo--!/O$^$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$>-o;:::~~~~
.....~~~~~~::::::;;;o-!!!?$$$$$$$$$$$$##$$$$$$$$$$$$$$$$$$$$$$$$$$$$$/-;:::~~~~
.......~~~~~~~::::::;;;o--|X$$@$$$$$$</>$$$$$$$$$$$$$$$$$$$$$$$$$$$$$>*o;::~~~~
.........~~~~~~~~:::::;;o!{/??|/%?||!!!?{$$$$$$$$$$$$$$$$$$$$$$$$$$$$$^o;::~~~~
...........~~~~~~~~~::::;?|ooooooooooo-!|X$$$$$$$$$$$$$$$$$$$$$$$$$$<!!o:::~~~~
..............~~~~~~~~:::::::;;;;;;;;;oo-|<<^$$$$$$$$$$$$$$$$$$$$$$&?-;;::~~~~~
................~~~~~~~~~~::::::::::;;;;oo!/#$$O$$$$$$$$$$$$$$$$<$$$O|;::~~~~~.
 ..................~~~~~~~~~~~~~:::::::;;o!$++||/$<$=#$$$$=X{/$|---!Oo;:~~~~~..
   ...................~~~~~~~~~~~~~~:::::;;;;oooo-!|%$$$$$$/!-oo;;;;:::~~~~~...
     ....................~~~~~~~~~~~~~~:::::::;;;o->=$$$$*X<-o;;::::~~~~~~~....
       ......................~~~~~~~~~~~~~~::::::;;o-!?>^?!o;;::::~~~~~~~......
         ........................~~~~~~~~~~~~~:::::;o-|&|?$o;::~~~~~~~~........
            .........................~~~~~~~~~~~~:::;-<o;;:::~~~~~~~...........
              ............................~~~~~~~~~~:::::~~~~~~~~..............
                 .............................~~~~~~~~~~~~~~~~.................
                    ..........................................................
                        ....................................................
                            ............................................
{% endhighlight %}

[jekyll-gh]: https://github.com/mojombo/jekyll
[jekyll]:    http://jekyllrb.com
