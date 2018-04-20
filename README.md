# 4d-tips-form-coordinates
"Open form window" relative to current screen

### Examples

* relative (with constants)

```
C_LONGINT($width;$height)
FORM GET PROPERTIES("Form1";$width;$height)

$screen:=Current_screen 

$w:=Open form window("Form1";Plain form window;\
	On_screen ($width;$height;$screen;Horizontally centered);\
	On_screen ($width;$height;$screen;Vertically centered))

DIALOG("Form1")
```

* with coordinates

```
C_LONGINT($width;$height)
FORM GET PROPERTIES("Form1";$width;$height)

$screen:=Current_screen 

$w:=Open form window("Form1";Plain form window;\
	On_screen (100;-1;$screen);\
	On_screen (-1;100;$screen))

DIALOG("Form1")
```

``Current_screen``

```
C_LONGINT($0;$screen)

C_LONGINT($window)
$window:=Current form window

C_LONGINT($wleft;$wtop;$wright;$wbottom)

If (0=$window)
	  //current screen for mouse position
	GET MOUSE($mouseX;$mouseY;$mouseB;*)
	$wleft:=$mouseX
	$wtop:=$mouseY
	$wright:=$wleft
	$wbottom:=$wtop
Else 
	  //current screen for current form
	GET WINDOW RECT($wleft;$wtop;$wright;$wbottom;$window)
End if 

  //find largest intersection
ARRAY LONGINT($intersection;0)
C_LONGINT($screen;$x;$y)
For ($screen;1;Count screens)
	C_LONGINT($sleft;$stop;$sright;$sbottom)
	SCREEN COORDINATES($sleft;$stop;$sright;$sbottom;$screen)
	$x:=Choose($sright<$wright;$sright;$wright)-Choose($sleft>$wleft;$sleft;$wleft)
	$x:=Choose($x>0;$x;0)
	$y:=Choose($sbottom<$wbottom;$sbottom;$wbottom)-Choose($stop>$wtop;$stop;$wtop)
	$y:=Choose($y>0;$y;0)
	APPEND TO ARRAY($intersection;$x*$y)
End for 

$0:=Choose(Count screens=1;Menu bar screen;Find in array($intersection;Max($intersection)))
```

``On_screen``

```
C_LONGINT($1;$width;$2;$height;$3;$screen;$4;$selector)
C_LONGINT($0;$value)

C_LONGINT($left;$top;$right;$bottom)
SCREEN COORDINATES($left;$top;$right;$bottom;$screen)

$params:=Count parameters

Case of 
	: ($params=4)
		
		$width:=$1
		$height:=$2
		$screen:=$3
		$selector:=$4
		
		Case of 
			: ($selector=Vertically centered)
				$value:=$top+(($bottom-$top)/2)-($height/2)
			: ($selector=At the top)
				$value:=$top
			: ($selector=At the bottom)
				$value:=$bottom
			: ($selector=On the right)
				$value:=$right
			: ($selector=On the left)
				$value:=$left
			: ($selector=Horizontally centered)
				$value:=$left+(($right-$left)/2)-($width/2)
			Else 
				$value:=$selector
		End case 
		
	: ($params=3)
		
		$x:=$1
		$y:=$2
		$screen:=$3
		
		Case of 
			: ($x<0) & ($y>=0)
				$value:=$top+$y
			: ($y<0) & ($x>=0)
				$value:=$left+$x
		End case 
		
End case 

$0:=$value
```
