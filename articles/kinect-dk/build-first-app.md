---
title: Rövid útmutató – az első Azure Kinect-alkalmazás létrehozása
description: Ez a rövid útmutató végigvezeti az Azure Kinect DK-felhasználónak egy új alkalmazás létrehozásának folyamatán.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: Kinect, Azure, szenzor, SDK, mikrofon, hozzáférési mikrofon, MIC-információ
ms.openlocfilehash: 3632145b3f3b63023e0c66e3cf99903231802edf
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277953"
---
# <a name="quickstart-build-your-first-azure-kinect-application"></a>Rövid útmutató: az első Azure Kinect-alkalmazás létrehozása

Bevezetés az Azure Kinect DK használatába? Ezzel a rövid útmutatóval megkezdheti és futtathatja az eszközt!

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

A következő függvények tartoznak ide:

- [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc)
- [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
- [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22)
- [`k4a_device_start_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
- [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
- [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)

## <a name="prerequisites"></a>Előfeltételek

1. [Állítsa be az Azure Kinect DK-eszközt](set-up-azure-kinect-dk.md).
2. [Töltse le](sensor-sdk-download.md) és telepítse az Azure Kinect Sensor SDK-t.

## <a name="headers"></a>Fejlécek

Csak egy fejlécre lesz szüksége, és ez `k4a.h` . Győződjön meg arról, hogy a választott fordító az SDK lib-vel van beállítva, és mappákat is tartalmaz. Szüksége lesz a és a `k4a.lib` `k4a.dll` csatolt fájlokra is. Érdemes lehet megtekinteni [Az Azure Kinect-függvénytár hozzáadását a projekthez](add-library-to-project.md).

```C
#include <k4a/k4a.h>
```

## <a name="finding-an-azure-kinect-dk-device"></a>Azure Kinect DK-eszköz megkeresése

Több Azure Kinect DK-eszköz is csatlakoztatható a számítógéphez. Először is kezdjük azzal, hogy megtalálják, hogy hány, vagy ha bármelyik csatlakozik a [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc) függvény használatával. A függvénynek azonnal működnie kell, további telepítés nélkül.

```C
uint32_t count = k4a_device_get_installed_count();
```

Miután meghatározta, hogy van-e csatlakoztatott eszköz a számítógéphez, megnyithatja azt a használatával [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) . Megadhatja a megnyitni kívánt eszköz indexét, vagy használhatja az `K4A_DEVICE_DEFAULT` elsőt.

```C
// Open the first plugged in Kinect device
k4a_device_t device = NULL;
k4a_device_open(K4A_DEVICE_DEFAULT, &device);
```
Ahogy a legtöbb esetben az Azure Kinect Library-ben, amikor megnyit valamit, be kell fejeznie azt is, ha elkészült vele! A leállítás után ne felejtsen el hívni a következőre: [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423) .

```C
k4a_device_close(device);
```

Az eszköz megnyitását követően tesztelést végezhetünk a működésének biztosításához. Tehát olvassa el az eszköz sorozatszámát!

```C
// Get the size of the serial number
size_t serial_size = 0;
k4a_device_get_serialnum(device, NULL, &serial_size);

// Allocate memory for the serial, then acquire it
char *serial = (char*)(malloc(serial_size));
k4a_device_get_serialnum(device, serial, &serial_size);
printf("Opened device: %s\n", serial);
free(serial);
```

## <a name="starting-the-cameras"></a>A kamerák elindítása

Miután megnyitotta az eszközt, a kamerát egy objektummal kell konfigurálnia [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) . A kamera-konfiguráció számos különböző lehetőséggel rendelkezik. Válassza ki azokat a beállításokat, amelyek a legjobban illeszkednek a saját forgatókönyvéhez.

```C
// Configure a stream of 4096x3072 BRGA color data at 15 frames per second
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps       = K4A_FRAMES_PER_SECOND_15;
config.color_format     = K4A_IMAGE_FORMAT_COLOR_BGRA32;
config.color_resolution = K4A_COLOR_RESOLUTION_3072P;

// Start the camera with the given configuration
k4a_device_start_cameras(device, &config);

// ...Camera capture and application specific code would go here...

// Shut down the camera when finished with application logic
k4a_device_stop_cameras(device);
```

## <a name="error-handling"></a>Hibakezelés

A rövid és egyértelműség kedvéért a hibajelentések nem jelennek meg néhány beágyazott példában. A hibakezelés azonban mindig fontos! Számos függvény egy általános sikerességi/meghibásodási típust ad vissza [`k4a_result_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga4b419a99aa2220b076a4520dc2afd1e5.html#ga4b419a99aa2220b076a4520dc2afd1e5) , vagy egy konkrétabb változatot, amely részletes információkkal szolgál, például: [`k4a_wait_result_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga44c7c0c1cfba7c879e9e2da1a869e4ee.html#ga44c7c0c1cfba7c879e9e2da1a869e4ee) . Ellenőrizze az egyes függvények dokumentációját vagy IntelliSense-adatait, és tekintse meg, hogy milyen hibaüzenetek várnak el belőle!

A [`K4A_SUCCEEDED`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___macros_ga8e5b48150bc243c6052793bd830c2fcd.html#ga8e5b48150bc243c6052793bd830c2fcd) és a [`K4A_FAILED`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___macros_ga7c2e32349135d008b6f836c571d434b4.html#ga7c2e32349135d008b6f836c571d434b4) makrók segítségével megtekintheti a függvények eredményét. Tehát ahelyett, hogy csak egy Azure Kinect DK-eszközt nyisson meg, az alábbihoz hasonló módon tudjuk védeni a függvényt:

```C
// Open the first plugged in Kinect device
k4a_device_t device = NULL;
if ( K4A_FAILED( k4a_device_open(K4A_DEVICE_DEFAULT, &device) ) )
{
    printf("Failed to open k4a device!\n");
    return;
}
```

## <a name="full-source"></a>Teljes forrás

```C
#pragma comment(lib, "k4a.lib")
#include <k4a/k4a.h>

#include <stdio.h>
#include <stdlib.h>

int main()
{
    uint32_t count = k4a_device_get_installed_count();
    if (count == 0)
    {
        printf("No k4a devices attached!\n");
        return 1;
    }

    // Open the first plugged in Kinect device
    k4a_device_t device = NULL;
    if (K4A_FAILED(k4a_device_open(K4A_DEVICE_DEFAULT, &device)))
    {
        printf("Failed to open k4a device!\n");
        return 1;
    }

    // Get the size of the serial number
    size_t serial_size = 0;
    k4a_device_get_serialnum(device, NULL, &serial_size);

    // Allocate memory for the serial, then acquire it
    char *serial = (char*)(malloc(serial_size));
    k4a_device_get_serialnum(device, serial, &serial_size);
    printf("Opened device: %s\n", serial);
    free(serial);

    // Configure a stream of 4096x3072 BRGA color data at 15 frames per second
    k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
    config.camera_fps       = K4A_FRAMES_PER_SECOND_15;
    config.color_format     = K4A_IMAGE_FORMAT_COLOR_BGRA32;
    config.color_resolution = K4A_COLOR_RESOLUTION_3072P;

    // Start the camera with the given configuration
    if (K4A_FAILED(k4a_device_start_cameras(device, &config)))
    {
        printf("Failed to start cameras!\n");
        k4a_device_close(device);
        return 1;
    }

    // Camera capture and application specific code would go here

    // Shut down the camera when finished with application logic
    k4a_device_stop_cameras(device);
    k4a_device_close(device);

    return 0;
}

```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan kereshet és nyisson meg egy Azure Kinect DK-eszközt az Sensor SDK használatával
> [!div class="nextstepaction"]
>[Eszköz megkeresése és megnyitása](find-then-open-device.md)
