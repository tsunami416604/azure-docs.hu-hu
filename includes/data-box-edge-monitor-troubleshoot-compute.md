---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 7058d7f46373f8adaacbcbf90e5ea591a15f8f37
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58404075"
---
Egy Data Box peremhálózati eszköz, amely be van állítva, a számítási szerepkör docker egy részét a parancsok figyelése, vagy modulok hibaelhárítása érhető el. Az elérhető parancsok megtekintéséhez [csatlakozni a PowerShell-felületünkön](#connect-to-the-powershell-interface) , és használja a `dkrdbe` függvény.

```powershell
[10.100.10.10]: PS>dkrdbe -?
Usage: dkrdbe COMMAND

Commands:
   image [prune]
   images
   inspect
   login
   logout
   logs
   port
   ps
   pull
   start
   stats
   stop
   system [df]
   top

[10.100.10.10]: PS>
```
Az alábbi táblázatban az elérhető parancsok rövid leírását rendelkezik `dkrdbe`:

|command  |Leírás |
|---------|---------|
|`image`     | Lemezképek kezelése       |
|`images`     | Rendszerképek felsorolása         |
|`inspect`     | Docker-objektumokra vonatkozó alacsony szintű információkat ad vissza         |
|`login`     | Jelentkezzen be a Docker-tárolójegyzék         |
|`logout`     | A Docker-tárolójegyzék kijelentkezés         |
|`logs`     | A tároló naplóinak beolvasása        |
|`port`     | Lista portleképezéseit, vagy egy adott a tároló-leképezés        |
|`ps`     | Tárolók listázása        |
|`pull`     | Kérje le a képet, vagy egy tárház egy regisztrációs adatbázisból         |
|`start`     | Egy vagy több leállított tárolókat indítása         |
|`stats`     | Élő stream tároló(k) erőforrás-használati statisztikák megjelenítése         |
|`stop`     | Egy vagy több futó tárolók leállításához.        |
|`system`     | Docker kezelése         |
|`top`     | Megjeleníti egy adott tároló futó folyamatok         |

Minden rendelkezésre álló parancssori súgójának megjelenítéséhez használja a `dkrdbe <command-name> --help`.

A használatelemzés, például a `port` parancshoz, írja be:

```powershell
[10.100.10.10]: P> dkrdbe port --help

Usage:  dkr port CONTAINER [PRIVATE_PORT[/PROTO]]

List port mappings or a specific mapping for the container
[10.100.10.10]: P> dkrdbe login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
[10.100.10.10]: PS>
```

A rendelkezésre álló parancsokat is tartalmaz a `dkrdbe` függvény ugyanazokat a paramétereket használja, mint a normál docker parancs. Lépjen a beállítások és a docker paranccsal használt paraméterek [használja a Docker parancssori](https://docs.docker.com/engine/reference/commandline/docker/).

### <a name="to-check-if-the-module-deployed-successfully"></a>Ellenőrizze, hogy ha a modul üzembe helyezése sikeresen befejeződött

COMPUTE-modulok olyan tárolók, amelyek egy üzleti logikát implementálva. Ha egy számítási modul sikeres üzembe helyezését ellenőrzéséhez futtassa a `ps` parancsot, és ellenőrizze, hogy fut-e a tárolóban (a számítási modul megfelelő).

(Fel vannak függesztve, azokat is beleértve) az összes tárolót listájának beolvasása, futtassa a `ps -a` parancsot.

```powershell
[10.100.10.10]: P> dkrdbe ps -a
CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
[10.100.10.10]: PS>
```

Ha hiba történt a létrehozásakor a tároló rendszerképét, vagy a rendszerkép lekérése során, futtassa `logs edgeAgent`.  `EdgeAgent` az IoT Edge futásidejű tároló, amely az egyéb tároló-kiépítés van.

Mivel `logs edgeAgent` kiírja az összes napló, nézze meg, a legutóbbi hibákat a beállítás használata kiváló módszer `--tail 20`.


```powershell
[10.100.10.10]: PS>dkrdbe logs edgeAgent --tail 20
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Connected socket /var/run/iotedge/mgmt.sock
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Sending request http://mgmt.sock/modules?api-version=2018-06-28
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Getting edge agent config...
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Obtained edge agent config
2019-02-28 23:38:23.469 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Edgelet.ModuleManagementHttpClient] - Received a valid Http response from unix:///var/run/iotedge/mgmt.soc
k for List modules
--------------------CUT---------------------
--------------------CUT---------------------
08:28.1007774+00:00","restartCount":0,"lastRestartTimeUtc":"2019-02-26T20:08:28.1007774+00:00","runtimeStatus":"running","version":"1.0","status":"running","restartPolicy":"always
","type":"docker","settings":{"image":"edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64","imageHash":"sha256:47778be0602fb077d7bc2aaae9b0760fbfc7c058bf4df192f207ad6cbb96f7cc","c
reateOptions":"{\"HostConfig\":{\"Binds\":[\"/home/hcsshares/share4-dl460:/home/input\",\"/home/hcsshares/share4-iot:/home/output\"]}}"},"env":{}}
2019-02-28 23:38:28.480 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Planners.HealthRestartPlanner] - HealthRestartPlanner created Plan, with 0 command(s).
```

### <a name="to-get-container-logs"></a>A tároló naplóinak beolvasása

Egy adott tároló naplóinak beolvasása, első listában a tárolót, és kérje le a tároló, amely az Önt érdeklő a naplókat.

1. [Kapcsolódás a PowerShell-felületünkön](#connect-to-the-powershell-interface).
2. Futó tárolók listájának lekéréséhez futtassa a `ps` parancsot.

    ```powershell
    [10.100.10.10]: P> dkrdbe ps
    CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
    d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
    0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
    2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
    acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
    ```

3. Jegyezze fel a Tárolóazonosító van szüksége a naplókat tároló.

4. A naplók egy adott tároló lekéréséhez futtassa a `logs` parancsot, és adja a tároló azonosítója.

    ```powershell
    [10.100.10.10]: PS>dkrdbe logs d99e2f91d9a8
    02/26/2019 18:21:45: Info: Opening module client connection.
    02/26/2019 18:21:46: Info: Initializing with input: /home/input, output: /home/output.
    02/26/2019 18:21:46: Info: IoT Hub module client initialized.
    02/26/2019 18:22:24: Info: Received message: 1, SequenceNumber: 0 CorrelationId: , MessageId: 081886a07e694c4c8f245a80b96a252a Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\__Microsoft Data Box Edge__\\Upload\\Errors.xml","ShareName":"share4-dl460"}]
    02/26/2019 18:22:24: Info: Moving input file: /home/input/__Microsoft Data Box Edge__/Upload/Errors.xml to /home/output/__Microsoft Data Box Edge__/Upload/Errors.xml
    02/26/2019 18:22:24: Info: Processed event.
    02/26/2019 18:23:38: Info: Received message: 2, SequenceNumber: 0 CorrelationId: , MessageId: 30714d005eb048e7a4e7e3c22048cf20 Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\f [10]","ShareName":"share4-dl460"}]
    02/26/2019 18:23:38: Info: Moving input file: /home/input/f [10] to /home/output/f [10]
    02/26/2019 18:23:38: Info: Processed event.
    ```

### <a name="to-monitor-the-usage-statistics-of-the-device"></a>Az eszköz a használati statisztikák figyelése

Az eszközön a memória, Processzor- és i/o monitorozásához használja a `stats` parancsot.

1. [Kapcsolódás a PowerShell-felületünkön](#connect-to-the-powershell-interface).
2. Futtassa a `stats` paranccsal tiltsa le az élő streamet, és kérje le csak az első eredmény.

   ```powershell
   dkrdbe stats --no-stream
   ```

   Az alábbi példa bemutatja ennek a parancsmagnak a használatát:

    ```
    [10.100.10.10]: P> dkrdbe stats --no-stream
    CONTAINER ID        NAME          CPU %         MEM USAGE / LIMIT     MEM %         NET I/O             BLOCK I/O           PIDS
    d99e2f91d9a8        movefile      0.0           24.4MiB / 62.89GiB    0.04%         751kB / 497kB       299kB / 0B          14
    0a06f6d605e9        filemove      0.00%         24.11MiB / 62.89GiB   0.04%         679kB / 481kB       49.5MB / 0B         14
    2f8a36e629db        edgeHub       0.18%         173.8MiB / 62.89GiB   0.27%         4.58MB / 5.49MB     25.7MB / 2.19MB     241
    acce59f70d60        edgeAgent     0.00%         35.55MiB / 62.89GiB   0.06%         2.23MB / 2.31MB     55.7MB / 332kB      14
    [10.100.10.10]: PS>
    ```

