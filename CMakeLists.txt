idf_component_register(SRCS "SpotifyAPICall.c" "JsonExtraction.c" "JpegDecode.c" "SpotifyInterface.c" 
                            "SpotifyHttpLocalServer.c"
                    INCLUDE_DIRS "include"
                    
                    REQUIRES Utilities mdns esp_jpeg esp_wifi esp_event esp_eth protocol_examples_common
                    esp_http_client nvs_flash json
                    esp_psram heap) 
