---
title: 'Gyors útmutató: Azure Kinect Body Tracking-alkalmazás létrehozása'
description: Lépésenkénti útmutató az első Azure-beli Kinect Body Tracking alkalmazás létrehozásához
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: Kinect, Azure, szenzor, SDK, szövegtörzs, nyomon követés, közös, alkalmazás, első
ms.openlocfilehash: bdf8ee7a14bf59a151dfa316b11159830b4f63b8
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "85277960"
---
# <a name="quickstart-build-an-azure-kinect-body-tracking-application"></a>Gyors útmutató: Azure Kinect Body Tracking-alkalmazás létrehozása

Első lépések a Body Tracking SDK-val? Ez a rövid útmutató a Body Tracking szolgáltatással működik. További példákat ebben az [Azure-Kinect-Sample](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples)adattárban találhat.

## <a name="prerequisites"></a>Előfeltételek

- [Az Azure Kinect DK beállítása](set-up-azure-kinect-dk.md)
- [A Body Tracking SDK beállítása](body-sdk-setup.md)
- Útmutató az [első Azure-beli Kinect-alkalmazás létrehozásához](build-first-app.md) .
- Ismerkedjen meg a következő Sensor SDK-funkciókkal:
  - [k4a_device_open ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
  - [k4a_device_start_cameras ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
  - [k4a_device_stop_cameras ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  - [k4a_device_close ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)
- Tekintse át a következő Body Tracking SDK-függvények dokumentációját:
  - [k4abt_tracker_create ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1aa71481b8441def94de11b29e0e3cbc.html#ga1aa71481b8441def94de11b29e0e3cbc)
  - [k4abt_tracker_enqueue_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e)
  - [k4abt_tracker_pop_result ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b)
  - [k4abt_tracker_shutdown ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga94036969ef94cbc414c78b3f6d04bfa5.html#ga94036969ef94cbc414c78b3f6d04bfa5)
  - [k4abt_tracker_destroy ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gab2c9afca092130976cd66077c0557ed1.html#gab2c9afca092130976cd66077c0557ed1)

## <a name="headers"></a>Fejlécek

A törzs nyomon követése egyetlen fejlécet használ `k4abt.h` . Adja meg a fejlécet a következő mellett: `k4a.h` . Győződjön meg arról, hogy a választott fordító beállítása mind az Sensor SDK, mind a Body Tracking SDK és mappák esetében be van állítva `lib` `include` . Emellett a és a fájlokat is csatolni kell `k4a.lib` `k4abt.lib` . Az alkalmazás futtatásához szükséges,,, `k4a.dll` `k4abt.dll` és az `onnxruntime.dll` `dnn_model.onnx` alkalmazások végrehajtási útvonalán kell lennie.

```C
#include <k4a/k4a.h>
#include <k4abt.h>
```

## <a name="open-device-and-start-the-camera"></a>Eszköz megnyitása és a kamera elindítása

Az első Body Tracking-alkalmazás feltételezi, hogy egyetlen Azure Kinect-eszköz csatlakozik a számítógéphez.

A Body Tracking az Sensor SDK-ra épül. A Body Tracking használatához először meg kell nyitnia és konfigurálnia kell az eszközt. Az [k4a_device_open ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) függvény használatával nyissa meg az eszközt, majd konfigurálja [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) objektummal. A legjobb eredmény érdekében állítsa a mélységi módot a vagy a értékre `K4A_DEPTH_MODE_NFOV_UNBINNED` `K4A_DEPTH_MODE_WFOV_2X2BINNED` . A törzs nyomon követése nem fog futni, ha a mélységi mód értéke `K4A_DEPTH_MODE_OFF` vagy `K4A_DEPTH_MODE_PASSIVE_IR` .

Az eszköz megkeresésével és megnyitásával kapcsolatos további információkért tekintse meg az [oldalt](find-then-open-device.md).

A következő lapokon talál további információt az Azure Kinect mélységi módjairól: [hardver-specifikáció](hardware-specification.md) és [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d) enumerálások.

```C
k4a_device_t device = NULL;
k4a_device_open(0, &device);

// Start camera. Make sure depth camera is enabled.
k4a_device_configuration_t deviceConfig = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
deviceConfig.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;
deviceConfig.color_resolution = K4A_COLOR_RESOLUTION_OFF;
k4a_device_start_cameras(device, &deviceConfig);
```

## <a name="create-the-tracker"></a>A Szemléző létrehozása

A törzs nyomon követési eredményeinek első lépése a törzs követésének létrehozása. Szüksége van az érzékelő kalibrálására [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) struktúrára. Az érzékelő kalibrálása a [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) függvény használatával kérdezhető le.

```C
k4a_calibration_t sensor_calibration;
k4a_device_get_calibration(device, deviceConfig.depth_mode, deviceConfig.color_resolution, &sensor_calibration);

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker);
```

## <a name="get-captures-from-the-azure-kinect-device"></a>Rögzítés az Azure Kinect-eszközről

A képadatok ezen a lapon való beolvasásával kapcsolatos további információkért tekintse meg a [következőt](retrieve-images.md):.

```C
// Capture a depth frame
k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS);
```

## <a name="enqueue-the-capture-and-pop-the-results"></a>A rögzítés sorba helyezni és a találatok beugró ablakának megjelenítése

A Szemléző belsőleg karbantart egy bemeneti várólistát és egy kimeneti várólistát az Azure Kinect DK-rögzítések aszinkron feldolgozásához. A következő lépés a `k4abt_tracker_enqueue_capture()` függvény használata új rögzítés a bemeneti várólistába való felvételéhez. A függvény használatával megnyithatja a `k4abt_tracker_pop_result()` kimeneti üzenetsor eredményét. Az időtúllépési érték az alkalmazástól függ, és az üzenetsor-kezelési várakozási időt vezérli.

Az első Body Tracking-alkalmazás a valós idejű feldolgozási mintát használja. A többi mintázat részletes ismertetését lásd a [törzs nyomon követésének eredményeiről](get-body-tracking-results.md) .

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

## <a name="access-the-body-tracking-result-data"></a>A törzs nyomon követési eredményeinek elérése

Az egyes érzékelők rögzítésének eredményét törzsi keretek [k4abt_frame_t](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/structk4abt__frame__t.html) struktúrája tárolja. Az egyes törzsi keretek három fő összetevőt tartalmaznak: a törzs struktúráinak gyűjteményét, a 2D-szövegtörzs indexének leképezését és a bemeneti rögzítést.

Az első Body Tracking alkalmazás csak az észlelt szervezetek számát éri el. A szövegtörzsben lévő információk részletes ismertetését lásd [: a szövegtörzsben tárolt adathozzáférés](access-data-body-frame.md) .

```C
size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
printf("%zu bodies are detected!\n", num_bodies);
```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Utolsó lépésként állítsa le a törzs nyomon követését, és szabadítsa fel a törzs nyomon követésére szolgáló objektumot. Emellett le kell állítania és be kell fejeznie az eszközt.

```C
k4abt_tracker_shutdown(tracker);
k4abt_tracker_destroy(tracker);
k4a_device_stop_cameras(device);
k4a_device_close(device);
```

## <a name="full-source"></a>Teljes forrás

```C
#include <stdio.h>
#include <stdlib.h>

#include <k4a/k4a.h>
#include <k4abt.h>

#define VERIFY(result, error)                                                                            \
    if(result != K4A_RESULT_SUCCEEDED)                                                                   \
    {                                                                                                    \
        printf("%s \n - (File: %s, Function: %s, Line: %d)\n", error, __FILE__, __FUNCTION__, __LINE__); \
        exit(1);                                                                                         \
    }                                                                                                    \

int main()
{
    k4a_device_t device = NULL;
    VERIFY(k4a_device_open(0, &device), "Open K4A Device failed!");

    // Start camera. Make sure depth camera is enabled.
    k4a_device_configuration_t deviceConfig = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
    deviceConfig.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;
    deviceConfig.color_resolution = K4A_COLOR_RESOLUTION_OFF;
    VERIFY(k4a_device_start_cameras(device, &deviceConfig), "Start K4A cameras failed!");

    k4a_calibration_t sensor_calibration;
    VERIFY(k4a_device_get_calibration(device, deviceConfig.depth_mode, deviceConfig.color_resolution, &sensor_calibration),
        "Get depth camera calibration failed!");

    k4abt_tracker_t tracker = NULL;
    k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
    VERIFY(k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker), "Body tracker initialization failed!");

    int frame_count = 0;
    do
    {
        k4a_capture_t sensor_capture;
        k4a_wait_result_t get_capture_result = k4a_device_get_capture(device, &sensor_capture, K4A_WAIT_INFINITE);
        if (get_capture_result == K4A_WAIT_RESULT_SUCCEEDED)
        {
            frame_count++;
            k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
            k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
            if (queue_capture_result == K4A_WAIT_RESULT_TIMEOUT)
            {
                // It should never hit timeout when K4A_WAIT_INFINITE is set.
                printf("Error! Add capture to tracker process queue timeout!\n");
                break;
            }
            else if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
            {
                printf("Error! Add capture to tracker process queue failed!\n");
                break;
            }

            k4abt_frame_t body_frame = NULL;
            k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
            if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
            {
                // Successfully popped the body tracking result. Start your processing

                size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
                printf("%zu bodies are detected!\n", num_bodies);

                k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
            }
            else if (pop_frame_result == K4A_WAIT_RESULT_TIMEOUT)
            {
                //  It should never hit timeout when K4A_WAIT_INFINITE is set.
                printf("Error! Pop body frame result timeout!\n");
                break;
            }
            else
            {
                printf("Pop body frame result failed!\n");
                break;
            }
        }
        else if (get_capture_result == K4A_WAIT_RESULT_TIMEOUT)
        {
            // It should never hit time out when K4A_WAIT_INFINITE is set.
            printf("Error! Get depth frame time out!\n");
            break;
        }
        else
        {
            printf("Get depth capture returned error: %d\n", get_capture_result);
            break;
        }

    } while (frame_count < 100);

    printf("Finished body tracking processing!\n");

    k4abt_tracker_shutdown(tracker);
    k4abt_tracker_destroy(tracker);
    k4a_device_stop_cameras(device);
    k4a_device_close(device);

    return 0;
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
>[Testkövetési eredmények lekérése](get-body-tracking-results.md)
