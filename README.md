# Documentation


https://github.com/tssovi/grokking-the-object-oriented-design-interview/blob/master/object-oriented-design-case-studies/design-a-restaurant-management-system.md
  package main

import (
	"fmt"
	"log"
	"math"
	"runtime"

	"github.com/go-gl/gl/v4.1-compatibility/gl"
	"github.com/go-gl/glfw/v3.3/glfw"
)

func initOpenGL() *glfw.Window {
	// Initialize glfw
	if err := glfw.Init(); err != nil {
		log.Fatalln("failed to initialize glfw:", err)
	}

	// Ensure the main thread runs OpenGL
	runtime.LockOSThread()

	// Create an OpenGL window
	window, err := glfw.CreateWindow(800, 600, "Glowing Globe", nil, nil)
	if err != nil {
		log.Fatalln("failed to create window:", err)
	}
	window.MakeContextCurrent()

	// Initialize OpenGL
	if err := gl.Init(); err != nil {
		log.Fatalln("failed to initialize OpenGL:", err)
	}

	// Set the OpenGL viewport
	gl.Viewport(0, 0, int32(window.GetWidth()), int32(window.GetHeight()))
	gl.ClearColor(0.0, 0.0, 0.0, 1.0) // Black background

	// Set up the perspective (camera)
	gl.MatrixMode(gl.PROJECTION)
	gl.LoadIdentity()
	gluPerspective(45.0, 1.0, 0.1, 100.0)

	// Move the camera a bit back
	gl.Translatef(0.0, 0.0, -5.0)

	return window
}

func drawSphere() {
	// Draw a simple sphere
	segments := 50
	radius := 1.0
	for i := 0; i < segments; i++ {
		lat0 := math.Pi * float64(i) / float64(segments)
		lat1 := math.Pi * float64(i+1) / float64(segments)

		gl.Begin(gl.QUAD_STRIP)
		for j := 0; j <= segments; j++ {
			lng := 2 * math.Pi * float64(j) / float64(segments)
			x0 := radius * math.Cos(lat0) * math.Cos(lng)
			y0 := radius * math.Cos(lat0) * math.Sin(lng)
			z0 := radius * math.Sin(lat0)
			gl.Vertex3f(float32(x0), float32(y0), float32(z0))

			x1 := radius * math.Cos(lat1) * math.Cos(lng)
			y1 := radius * math.Cos(lat1) * math.Sin(lng)
			z1 := radius * math.Sin(lat1)
			gl.Vertex3f(float32(x1), float32(y1), float32(z1))
		}
		gl.End()
	}
}

func setGlowMaterial() {
	// Set material properties (this will make the sphere appear to glow)
	gl.Materialfv(gl.FRONT, gl.EMISSION, []float32{0.5, 0.5, 1.0, 1.0}) // Blue glow
	gl.Materialfv(gl.FRONT, gl.DIFFUSE, []float32{1.0, 1.0, 1.0, 1.0})  // White diffuse
}

func main() {
	window := initOpenGL()

	// Main render loop
	for !window.ShouldClose() {
		// Clear the screen
		gl.Clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT)

		// Set glow material
		setGlowMaterial()

		// Draw the glowing sphere
		drawSphere()

		// Swap buffers and poll events
	
