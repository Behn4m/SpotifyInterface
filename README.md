# Spotify Interface Module

This module facilitates Spotify authorization and communication with the Spotify web service using ESP-IDF (Espressif IoT Development Framework).

## Table of Contents

- [Introduction](#introduction)
- [File Structure](#file-structure)
- [Dependencies](#dependencies)
- [Features](#features)
- [API Reference](#api-reference)
- [How to use it](#how-to-use-it)

## Introduction

The Spotify Interface Module is designed to handle authorization and communication with the Spotify service on embedded systems, particularly using ESP-IDF.
This component has following inquiries to compile: `esp_jpeg`, `nvsFlash`, `mdns`, `esp_wifi`, `esp_event`, `protocol_examples_common`, `esp_https_server`, `esp_http_client`, `nvs_flash` `json` `esp_psram` `heap`

## File Structure

- `SpotifyInterface.c`: Implementation file containing the implementation of functions defined in the header and private functions used to handle tasks.
- `SpotifyMakeRequest.c`: Implementation of the functions which make web requests needed to communicate with the Spotify Web Service.
- `JsonExtraction.c`: Implementation of functions to extract keys from Spotify responses.
- `SpotifyInterface.h`: Header file containing function prototypes and structure definitions.
- `SpotifyMakeRequest.h`: Header file containing function prototypes.
- `JsonExtraction.h`: Header file containing function prototypes.
- `JpegDecode.h`: Header file containing function prototypes for decoding jpeg file.
- `CMakeLists.txt`: Includes the dependencies, directories, and other CMake configurations.

## Dependencies

- ESP-IDF: Espressif IoT Development Framework
- Other libraries and modules as specified in the `#include` directives within the header file.

## Features

- Authorization with Spotify API.
- Handling user information and tokens securely.
- Sending commands to Spotify (play, pause, stop, next, previous, etc.).

## API Reference

**Spotify_TaskInit:** Checks if all mandatory fileds are addressed

```c
bool Spotify_TaskInit(SpotifyInterfaceHandler_t *SpotifyInterfaceHandler);
```

**Spotify_SendCommand:** Gets the command and sends releavant request to Spotify web service

```c
bool Spotify_SendCommand(SpotifyInterfaceHandler_t *SpotifyInterfaceHandler, int command);
```


## How to use it

This guide provides instructions on how to use the Spotify Interface Handler module in your project. The Spotify Interface Handler facilitates interactions with the Spotify service, allowing you to perform various operations like sending commands, managing tokens, and checking the service's status.

**Step 1:** Include Header File

```c
#include "https_spotify.h"
```

**Step 2:** Create an object instance

```c
SpotifyInterfaceHandler_t SpotifyInterfaceHandler;
```

**Step 3:** Initialize the handler

```c
SpotifyInterfaceHandler.IsSpotifyAuthorizedSemaphore = &IsSpotifyAuthorizedSemaphore;
SpotifyInterfaceHandler.ConfigAddressInSpiffs = SpotifyConfigAddressInSpiffs;
```

**Step 4:** Call Initialization Function

```c
if (Spotify_TaskInit(&SpotifyInterfaceHandler))
{
    // Handler initialized successfully
} else 
{
    // Handler initialization failed, handle the error
}
```

**Step 5:** Check if Authorization is done

```c
if (xSemaphoreTake(IsSpotifyAuthorizedSemaphore, portMAX_DELAY) == pdTRUE)
    {
        // Spotify is Authorized
    }
```

**Step 6:** Use APIs to send Get Information and Playback commands to Spotify
```c
    bool CommandResult = false;
    CommandResult = Spotify_SendCommand(SpotifyInterfaceHandler, GetUserInfo);
    if (CommandResult == true)
    {
        ESP_LOGI(TAG, "User info updated");
    }

    vTaskDelay((pdMS_TO_TICKS(SEC * 1)));
    CommandResult = Spotify_SendCommand(SpotifyInterfaceHandler, GetNowPlaying);
    if (CommandResult == true)
    {
        ESP_LOGI(TAG, "Playback info updated");

        vTaskDelay((pdMS_TO_TICKS(SEC * 2)));
        CommandResult = Spotify_SendCommand(SpotifyInterfaceHandler, Play);
    }
```

**Step 7:** Download and decode the song cover photo (it is recommended to do this only if the song is changed)
```c
    bool isNewSong = strcmp(SpotifyInterfaceHandler.PlaybackInfo->SongImageURL, &imgLink);
    if (isNewSong)
    {
        CommandResult = Spotify_SendCommand(SpotifyInterfaceHandler, GetCoverPhoto);
        vTaskDelay(pdMS_TO_TICKS(100));
        if (CommandResult == false)
        {
            ESP_LOGE(TAG, "Cover photo update failed");
            return;
        }
        strcpy(imgLink, SpotifyInterfaceHandler.PlaybackInfo->SongImageURL);
    }

```

## How to add
Open the terminal, navigate to the parent directory (the directory of your project to you want to add this submodule as a component it) and run the command below:

```c
git submodule add -f https://github.com/Behn4m/SpotifyInterface components/SpotifyInterface
```

## Code structure:

![Diagram](/components/SpotifyInterface/documents/SpotifyInterface%20structure.png)
