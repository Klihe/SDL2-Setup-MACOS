# SDL2 for MACOS
#### It's can be also for C (with small changes)

### Stracture of Project
```tree
├── Makefile
├── build
│   └── debug
├── include
│   ├── SDL2 -> /opt/homebrew/Cellar/sdl2/2.30.0/include/SDL2
│   ├── SDL2_image -> /opt/homebrew/Cellar/sdl2_image/2.8.2/include/SDL2
│   └── SDL2_ttf -> /opt/homebrew/Cellar/sdl2_ttf/2.22.0/include
├── lib
│   ├── SDL2 -> /opt/homebrew/Cellar/sdl2/2.30.0/lib
│   ├── SDL2_image -> /opt/homebrew/Cellar/sdl2_image/2.8.2/lib
│   └── SDL2_ttf -> /opt/homebrew/Cellar/sdl2_ttf/2.22.0/lib
└── src
    └── main.c
```
# Steps

1. Install brew:\
    `"/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`
   
2. `brew install sdl2`\
   `brew install sdl2_image`\
   `brew install sdl2_ttf`

3. Open the project file terminal

4. Create project layout: `mkdir -p build/debug include lib src`
   
5. Finding the location of the sdl2:\
    `brew list sdl2`\
    `brew list sdl2_image`\
    `brew list sdl2_ttf`

6. My location of files:
    ## SDL2
    ```
    /opt/homebrew/Cellar/sdl2/2.30.0/bin/sdl2-config
    /opt/homebrew/Cellar/sdl2/2.30.0/include/SDL2/ (78 files)
    /opt/homebrew/Cellar/sdl2/2.30.0/lib/libSDL2-2.0.0.dylib
    /opt/homebrew/Cellar/sdl2/2.30.0/lib/cmake/ (2 files)
    /opt/homebrew/Cellar/sdl2/2.30.0/lib/pkgconfig/sdl2.pc
    /opt/homebrew/Cellar/sdl2/2.30.0/lib/ (4 other files)
    /opt/homebrew/Cellar/sdl2/2.30.0/share/aclocal/sdl2.m4
    ```
    ## SDL2_image
    ```
    /opt/homebrew/Cellar/sdl2_image/2.8.2/include/SDL2/SDL_image.h
    /opt/homebrew/Cellar/sdl2_image/2.8.2/lib/libSDL2_image-2.0.0.dylib
    /opt/homebrew/Cellar/sdl2_image/2.8.2/lib/cmake/ (2 files)
    /opt/homebrew/Cellar/sdl2_image/2.8.2/lib/pkgconfig/SDL2_image.pc
    /opt/homebrew/Cellar/sdl2_image/2.8.2/lib/ (2 other files)
    ```
    ## SDL2_ttf
    ```
    /opt/homebrew/Cellar/sdl2_ttf/2.22.0/include/SDL2/SDL_ttf.h
    /opt/homebrew/Cellar/sdl2_ttf/2.22.0/lib/libSDL2_ttf-2.0.0.dylib
    /opt/homebrew/Cellar/sdl2_ttf/2.22.0/lib/cmake/ (2 files)
    /opt/homebrew/Cellar/sdl2_ttf/2.22.0/lib/pkgconfig/SDL2_ttf.pc
    /opt/homebrew/Cellar/sdl2_ttf/2.22.0/lib/ (2 other files)
    ```

7. Linking sdl2 files:
   ## SDL2
   into include: `ln -s /opt/homebrew/Cellar/sdl2/2.30.0/include/SDL2 ./include/SDL2`\
   into lib: `ln -s /opt/homebrew/Cellar/sdl2/2.30.0/lib ./lib/SDL2`
   ## SDL2_image
    into include: `ln -s /opt/homebrew/Cellar/sdl2_image/2.8.2/include/SDL2 ./include/SDL2_image`\
    into lib: `ln -s /opt/homebrew/Cellar/sdl2_image/2.8.2/lib ./lib/SDL2_image`
   ## SDL2_ttf
    into include: `ln -s /opt/homebrew/Cellar/sdl2_ttf/2.22.0/include/SDL2 ./include/SDL2_ttf`\
    into lib: `ln -s /opt/homebrew/Cellar/sdl2_ttf/2.22.0/lib ./lib/SDL2_ttf`

8.  Create main.pp and Makefile: 
    ```
    touch src/main.cpp
    touch Makefile
    ```
9.  Code for Makefile
    ```Makefile
    SRC_DIR = src
    BUILD_DIR = build/debug
    CC = g++
    SRC_FILES = $(wildcard $(SRC_DIR)/*.cpp)
    OBJ_NAME = play
    INCLUDE_PATHS = -I include/SDL2 -I include/SDL2_image -I include/SDL2_ttf
    LIBRARY_PATHS = -L lib/SDL2 -L lib/SDL2_image -L lib/SDL2_ttf
    COMPILER_FLAGS = -std=c++11 -Wall -O0 -g
    LINKER_FLAGS = -lSDL2 -lSDL2_image -lSDL2_ttf

all:
	$(CC) $(COMPILER_FLAGS) $(LINKER_FLAGS) $(INCLUDE_PATHS) $(LIBRARY_PATHS) $(SRC_FILES) -o $(BUILD_DIR)/$(OBJ_NAME)
    all:
        $(CC) $(COMPILER_FLAGS) $(LINKER_FLAGS) $(INCLUDE_PATHS) $(LIBRARY_PATHS) $(SRC_FILES) -o $(BUILD_DIR)/$(OBJ_NAME)
    ```

1.  Code for main.cpp
    ````cpp
    #include <SDL.h>
    #include <iostream>

    int main() {
        std::cout << "Hello, SDL!" << std::endl;

        // Initialize SDL
        if (SDL_Init(SDL_INIT_VIDEO) != 0) {
            std::cerr << "SDL_Init Error: " << SDL_GetError() << std::endl;
            return 1;
        }

        // Create a window
        SDL_Window* window = SDL_CreateWindow("SDL Window", SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 640, 480, SDL_WINDOW_SHOWN | SDL_WINDOW_RESIZABLE);
        if (window == nullptr) {
            std::cerr << "SDL_CreateWindow Error: " << SDL_GetError() << std::endl;
            SDL_Quit();
            return 1;
        }

        // Create a renderer
        SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, SDL_RENDERER_ACCELERATED | SDL_RENDERER_PRESENTVSYNC);
        if (renderer == nullptr) {
            std::cerr << "SDL_CreateRenderer Error: " << SDL_GetError() << std::endl;
            SDL_DestroyWindow(window);
            SDL_Quit();
            return 1;
        }

        SDL_Event e;
        bool quit = false;
        while (!quit){
            while (SDL_PollEvent(&e)){
                if (e.type == SDL_QUIT){
                    quit = true;
                }
                if (e.type == SDL_KEYDOWN){
                    quit = true;
                }
                if (e.type == SDL_MOUSEBUTTONDOWN){
                    quit = true;
                }
            }
            // Set render color to blue
            SDL_SetRenderDrawColor(renderer, 255, 255, 255, 255);

            // Clear the window with the render color
            SDL_RenderClear(renderer);

            // Update the screen
            SDL_RenderPresent(renderer);
        }

        // Clean up resources
        SDL_DestroyRenderer(renderer);
        SDL_DestroyWindow(window);
        SDL_Quit();

        return 0;
    }
2.  Test:\
    `make`\
    `.\build\debug\play`

# If you have white window on your screen everything is set.
