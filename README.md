# IGIS

IGIS provides a Swift object library running as a server on Linux for remote graphics via a browser client.

## Usage

### Library
In order to use the library, include this resource as a dependency in Package.swift

```swift
// swift-tools-version:4.2
// The swift-tools-version declares the minimum version of Swift required to build this package.

import PackageDescription

let package = Package(
    name: "IgisShell",
    dependencies: [
      .package(url: "https://github.com/TangoGolfDigital/Igis.git", from:"0.0.85"),
    ],
    targets: [
        // Targets are the basic building blocks of a package. A target can define a module or a test suite.
        // Targets can depend on other targets in this package, and on products in packages which this package depends on.
        .target(
            name: "IgisShell",
            dependencies: ["Igis"]),
    ]
)
```

### Point
```swift
// Point Definition
public init(x:Int, y:Int)

public mutating func moveBy(offsetX:Int, offsetY:Int)

public mutating func moveTo(x:Int, y:Int)
```

### DoublePoint
```swift
// DoublePoint Definition
public init(x:Double, y:Double)

public mutating func moveBy(offsetX:Double, offsetY:Double)

public mutating func moveTo(x:Double, y:Double)
```

### Size
```swift
// Size Definition
public init(width:Int, height:Int)

public mutating func enlargeBy(changeWidth:Int, changeHeight:Int)

public mutating func changeTo(width:Int, height:Int)
```

### Rect
```swift
// Rect Definition
public init(topLeft:Point, size:Size)
```

### Color
```swift
// Color Definition
public init (red:UInt8, green:UInt8, blue:UInt8)

public init(_ name:Name)
```
Note:  Color names are from [w3 org](https://www.w3.org/TR/css-color-3/).  If a name is not available it has an available synonym.  For example "grey" can be found by using "gray", and "aqua" can be found by using "cyan".

### StrokeStyle
```swift
// StrokeStyle Definition
public init(color:Color)
```

### FillStyle
```swift
// FillStyle Definition
 public init(color:Color)
 ```

### Painting 
The painting of all objects occurs on a "canvas".  The coordinate system of the canvas begins at (0,0) in the top-left corner.  X increases toward right, and Y increases toward the bottom.  Throughout this document, unless otherwise noted, all units are pixels.

**IMPORTANT:** Merely creating an object will not affect the display on the Canvas.
In order for an object to be visible it must be painted on the canvas.

```swift
canvas.paint(helloWorld)
```

Most objects can be created at anytime, however they may only be painted in an event which provides a Canvas as a parameter.  The [IgisShell project](https://github.com/TangoGolfDigital/IgisShell) provides a useful shell to get started.  In order to start Igis, a class implementing the PainterProtocol is required:

```swift
class Painter : PainterProtocol {
    required init() {
    }

    func setup(canvas:Canvas) {
    }

    func update(canvas:Canvas) {

    }

    func onClick(canvas:Canvas, location:Point) {
    }
}
```

In order to start Igis, the type of class is passed as an argument to Igis's run function:
```swift
print("Starting...")
do {
    let igis = Igis()
    try igis.run(painterType:Painter.self)
} catch (let error) {
    print("Error: \(error)")
}
```

### Text
```swift
// Text Definition
public init(location:Point, text:String, font:String?=nil, fillMode:FillMode = .fill)
```

```swift
// Create a text object to be painted at location (x:100, y:100) using the current font 
// and current fill color.
// By default, the text will be filled.
let helloWorld = Text(location:Point(x:100, y:100), text:"Hello, World!") 
```

```swift
// Create a text object with a specific font using the current fill color.
let helloWorld = Text(location:Point(x:100, y:100), text:"Hello, World!", font:"50pt Arial bold") 
```

```swift
// Stroke the text using the current stroke color rather than fill.
let helloWorld = Text(location:Point(x:100, y:100), text:"Hello, World!", font:"50pt Arial bold", fillMode:.stroke) 
```

### Rectangle
```swift
// Rectangle Definition
public init(rect:Rect, fillMode:FillMode = .fill)
```

```swift
// Create a rectangle to be painted at location (x:100, y:100) of width 200 and height 100
// using the current fill color.
let box = Rectangle(rect:Rect(topLeft:Point(x:100, y:100), size:Size(width:200, height:100)))
```

```swift
// Create a rectangle to be stroked and filled using the current stroke color and current fill color.
let box = Rectangle(rect:Rect(topLeft:Point(x:100, y:100), size:Size(width:200, height:100)), fillMode:.fillAndStroke)
```

### Ellipse
```swift
// Ellipse Definition
public init(center:Point, radiusX:Int, radiusY:Int, 
rotation:Double=0.0, startAngle:Double=0.0, endAngle:Double=2.0*Double.pi,                                  
antiClockwise:Bool=false, fillMode:FillMode = .stroke)
```

### Lines
```swift
// Lines Definition
public init(from:Point, to:Point)

public func moveTo(_ point:Point) // Moves to the specified point without drawing

public func lineTo(_ point:Point) // Draws a line to the specified point

```

### Turtle Graphics
In addition to the above objects, Igis supports traditional turtle graphics.  For turtle graphics, the coordinate system is different.  In this coordinate system, the center of the canvas is labeled as the origin (0,0), termed "home".  When the turtle is in the home position, it is oriented up.  Rotating to the right rotates the turtle clockwise from north the specified number of degreess, rotating left rotates the turtle counter-clockwise the specified number of degrees.  The turtle can also move forwards or backwards a specified number of steps in the direction in which it is currently pointed.  

Note:  It's OK to mix objects and turtle graphics.

```swift
// Turtle Definition
public init(canvasSize:Size)

public func forward(steps:Int) // Move forward the specified number of steps

public func backward(steps:Int) // Move backward the specified number of steps

public func left(degrees:Double) // Turn left (counter-clockwise) the specified number of degrees

public func right(degrees:Double) // Turn right (clockwise) the specified number of degrees

public func penUp() // Lift the pen.  Subsequent movement will not be visible.

public func penDown() // Drop the pen.  Subsequent movement will be visible.

public func penColor(color:Color) // Set the color of the pen

public func penWidth(width:Int) // Set the width of the pen

public func push() // Push the current position, angle, and pen attributes onto the stack

public func pop() // Pop the previously pushed position, angle, and pen attributes

public func home() // Return to the home position
```