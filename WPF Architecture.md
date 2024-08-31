## WPF Architecture Overview

The Windows Presentation Foundation (WPF) architecture consists of three main layers:

1. **Managed Layer**
2. **Unmanaged Layer** 
3. **Core Operating System Layer**

### Managed Layer

The managed layer is written in managed code and includes the following major components:

1. **PresentationFramework**
   - Implements core UI elements like `Button`, `TextBox`, `ComboBox` with built-in support for data binding, styling and templating[1][2]
   - Provides robust data binding through the `Binding` class with support for various binding modes, data validation, conversion and updating[2]
   - Defines UI element appearance through `Style` for consistent styling across the application[2]
   - Implements animations declaratively in XAML using classes like `Storyboard` and `Animation`[2]

2. **PresentationCore** 
   - Manages visual elements and rendering through the `Visual` and `UIElement` classes[2]
   - Handles creation and management of rendering commands via `DrawingContext` for drawing shapes, text and images[2]

3. **WindowBase**
   - Provides window management functionality for creating, interacting with and managing window state[2]
   - Manages user input events like mouse, keyboard and touch and routes them to visual elements[2]

### Unmanaged Layer

The unmanaged layer contains the following components:

1. **MilCore (Media Integration Layer)**
   - Handles low-level rendering operations and manages interaction with DirectX for drawing graphics[1][3]
   - Integrates with DirectX to leverage GPU acceleration for rendering tasks and advanced visual effects[3]

2. **WindowsCodecs**
   - Utilizes the Windows Imaging Component (WIC) to decode various image formats into a format WPF can render[3]
   - Works with DirectX for efficient image processing and rendering[3]

### Core Operating System Layer

The core operating system layer includes:

1. **User32** 
   - Provides APIs for managing windows, user input and UI-related tasks at the OS level[3]
   - Processes and dispatches input events to application windows and controls[3]

2. **GDI (Graphics Device Interface)**
   - Provides APIs for 2D graphics drawing and device contexts[3]
   - Used for rendering operations involving 2D graphics like drawing text, shapes and images[3]

3. **CLR (Common Language Runtime)**
   - Manages memory allocation, garbage collection and object lifetime for .NET applications[3]
   - Provides the runtime environment for executing .NET code including JIT compilation, exception handling and type safety[3]

4. **Direct3D**
   - Provides APIs for rendering 3D graphics with the GPU[3]
   - Utilizes the GPU to accelerate rendering tasks for high-performance graphics processing[3]

5. **Device Drivers**
   - Manages communication between the OS and hardware devices like graphics cards[3]
   - Handles device initialization, resource allocation and device-specific functionality[3]

6. **Graphics Card**
   - Provides the physical hardware for rendering graphics including GPUs[3]
   - Executes graphics operations leveraging the GPU's parallel processing capabilities[3]

The managed layer interacts with the unmanaged layer, where MilCore and WindowsCodecs work with Direct3D for rendering and image processing[2][3]. The CLR utilizes User32 for UI management and event handling, while User32 uses GDI for certain 2D graphics operations[3]. Direct3D interfaces with Device Drivers for hardware resource management which communicates with the Graphics Card for rendering and GPU processing[3].
