---
title: Automatizálja az operációs rendszer és a keretrendszert az Azure Container Registry feladatok (ACR-feladatok) javítása
description: ACR-feladatokat, egy csomag az Azure Container Registry védelmét biztosító funkcióit bemutató automatikus tároló-rendszerkép összeállítását és a javítással, a felhőben.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: danlep
ms.openlocfilehash: cd2b14dc29f865a162cb1ced605e740a96f7a46a
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329973"
---
# <a name="automate-os-and-framework-patching-with-acr-tasks"></a>Az operációs rendszer és a keretrendszer javítás ACR feladatok automatizálása

Tárolók adja meg az új szintet jelentenek a virtualizálás, alkalmazás és a fejlesztői függőségek infrastruktúra-és működési követelmények elkülönítése. Milyen állapotban marad, azonban szükség, cím, hogyan van az application virtualization tudjon fókuszálni.

## <a name="what-is-acr-tasks"></a>Mi az ACR-feladatok?

**ACR-feladatok** Azure Container Registry lévő szolgáltatások együttese. A Linux, Windows és ARM biztosít a felhőalapú tároló-lemezkép létrehozása, és automatizálható [operációs rendszer és a keretrendszer javítása](#automate-os-and-framework-patching) a Docker-tárolók számára. ACR-feladatok nemcsak terjeszti ki a "belső-hurkot" fejlesztési ciklus a felhőre való igény szerinti tároló rendszerképek létrehozásának, de lehetővé teszi a forrás kód véglegesítésére, vagy egy tároló alaplemezkép frissítésekor az automatizált buildekig. Az alaplemezkép frissítés eseményindítók, automatizálhatja az operációs rendszer és alkalmazási keretrendszer munkafolyamat, biztonságos környezetben fenntartása a rendszerbiztonsági tagok nem módosítható a tárolók megtartásával javítása.

Hozhat létre, és tesztelje a tárolórendszerképek az ACR-feladatok négy módon:

* [Gyors feladat](#quick-task): Hozhat létre, és küldje le a tároló lemezképek igény szerinti, az Azure-ban, egy helyi Docker-motor telepítése nélkül. Úgy gondolja, hogy `docker build`, `docker push` a felhőben. Helyi forráskód és a egy Git-tárház készíthető.
* [Forrás kód véglegesítésére épülnek](#automatic-build-on-source-code-commit): Automatikusan indítható el egy tároló-rendszerkép összeállítását, ha a kód számára fontos, hogy egy Git-tárház.
* [A rendszerkép alapszintű frissítésének létrehozása](#automate-os-and-framework-patching): Egy tároló-rendszerkép összeállítását indítható el, ha a lemezképet alaplemezkép frissítve lett.
* [Több lépésből álló feladatokat](#multi-step-tasks-preview) (előzetes verzió): Adja meg a lemezképeket, a tárolók parancsok futnak, és a rendszerképek leküldése a beállításjegyzék több lépésből álló feladatokat. Ezt az ACR-feladatok előzetes funkciót támogatja az igény szerinti feladat-végrehajtás és párhuzamos rendszerkép összeállítását, teszteléséhez, és fejlessze a műveletek.

## <a name="quick-task"></a>A feladat kész

A belső-hurok szoftverfejlesztési ciklusokat, a kódírás, létrehozását és a forráskezelőhöz, véglegesítése előtt az alkalmazás tesztelése a megjelenítettnél valójában tároló életciklus-felügyelet elejére.

Az első sort véglegesíti code, ACR feladatok [gyors feladat](container-registry-tutorial-quick-task.md) szolgáltatást tud biztosítani az Azure-bA összeállít egy integrált fejlesztői felület a tárolórendszerkép felé történő kiszervezésével. A gyors feladatok ellenőrizheti, hogy az automatikus definíciókat hozhat létre, és nézze át a potenciális problémákat, a kód véglegesítése előtt.

Az ismert `docker build` formátum, a [az acr-build] [ az-acr-build] paranccsal az Azure CLI-ben egy *környezet* (fájlok halmazát hozhat létre), elküldi ACR feladatokat, és alapértelmezés szerint az összeállított rendszerképet leküldi a beállításjegyzékbe befejezését követően.

Az alábbi táblázatban néhány példa a támogatott környezeti helyek az ACR-feladatokat jeleníti meg:

| Környezet helye | Leírás | Példa |
| ---------------- | ----------- | ------- |
| Helyi fájlrendszer | A helyi fájlrendszer egy könyvtárban lévő fájlokat. | `/home/user/projects/myapp` |
| GitHub "master" ága | A fő (vagy más alapértelmezett) lévő fájlok egy GitHub-adattár ágában.  | `https://github.com/gituser/myapp-repo.git` |
| GitHub-ág | Adott ághoz egy GitHub-adattár.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| A GitHub Pull-kérelem | A lekéréses kérelem a GitHub-tárházba. | `https://github.com/gituser/myapp-repo.git#pull/23/head` |
| GitHub almappa | Egy GitHub-adattárban található almappában található fájlokat. Példa bemutatja a Pull-kérelem és almappák specifikáció kombinációja. | `https://github.com/gituser/myapp-repo.git#pull/24/head:myfolder` |
| Távoli tarball | A tömörített archívum egy távoli webkiszolgálón található fájlokat. | `http://remoteserver/myapp.tar.gz` |

ACR-tevékenységek, egy egyszerű tároló életciklus tervezték. ACR-feladatok például integrálása a CI/CD-megoldás. Végrehajtásával [az bejelentkezési] [ az-login] az egy [szolgáltatásnév][az-login-service-principal], CI/CD-megoldását sikerült majd ki [az acr builddel] [ az-acr-build] parancsok felhőplatformos termékeiért buildek kép.

Gyors feladatok használata ACR feladatok első oktatóanyaga, [hozhat létre tárolórendszerképeket a felhőben, az Azure Container Registry feladatok](container-registry-tutorial-quick-task.md).

## <a name="automatic-build-on-source-code-commit"></a>A forrás kód véglegesítésére automatikus build

ACR-feladatok használata aktiválják automatikusan egy tárolórendszerképet hozhat létre, amikor a kód számára fontos, hogy egy Git-tárház. Buildekkel kapcsolatos feladatok, konfigurálható az Azure CLI-paranccsal [az acr feladat][az-acr-task], lehetővé teszik egy Git-tárház és opcionálisan egy ágat és docker-fájlban adja meg. Csapata a tárházba kód véglegesítésére, amikor egy ACR-feladatok által létrehozott webhook aktiválja a tároló rendszerképét az adattárban lévő meghatározott build.

> [!IMPORTANT]
> Ha korábban már létrehozott feladatokat az előzetes verzióban az `az acr build-task` paranccsal, azokat a feladatokat újra létre kell hoznia az [az acr task][az-acr-task] paranccsal.

Ismerje meg, hogyan build aktiválása a forrás kód véglegesítés az ACR-feladatok második oktatóanyagban [automatizálása tárolórendszerképet hoz létre az Azure Container Registry feladatok](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizálja az operációs rendszer és a keretrendszer javítása

ACR-feladatok hatékonyságának valóban növelése érdekében a tároló létrehozási munkafolyamat annak képességére alapképet frissítés származik. A frissített alap rendszerképet leküldte a tárolójegyzékbe van, amikor ACR feladatok automatikusan hozhat létre minden olyan alkalmazás-lemezképek alapján.

Tárolórendszerképek széles körben kategorizálhatók *alap* lemezképek és *alkalmazás* lemezképek. Az alaplemezképek rendszerint az operációs rendszer és alkalmazási keretrendszerek, amelyen az alkalmazást a létrehozása mellett egyéb testreszabásokat. Ezek alaplemezképek önmagukban általában nyilvános felsőbb rétegbeli rendszerképen alapuló, például: [Az Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet], vagy [Node.js ][base-node]. Előfordulhat, hogy ossza meg az alkalmazás-lemezképek számos közös alapképet.

Egy operációs rendszer vagy alkalmazás keretrendszer-lemezkép frissítésekor a felsőbb rétegbeli karbantartó által, például egy kritikus fontosságú az operációs rendszer biztonsági javítás is frissítenie kell az alaplemezképek a kritikus fontosságú javítás szükséges. Minden egyes alkalmazás-lemezképet majd is újra kell építeni ezeket most már szerepel az alaprendszerképet a felsőbb rétegbeli javításokat tartalmazza.

ACR feladatok dinamikusan felderíti alaplemezkép függőségeit, amikor azt összeállít egy tárolórendszerképet, mert azt képes észlelni, amikor egy alkalmazás-lemezképet alaplemezkép frissül. Egy előre konfigurált [készítése feladatot](container-registry-tutorial-base-image-update.md#create-a-task), majd az ACR-feladatok **automatikusan újraépíti az összes alkalmazás-lemezképet** az Ön számára. Ezt automatikus észlelése és újraépítése, az ACR feladatok menti a frissített alaplemezkép hivatkozó kép, az idő és munka, általában az manuálisan nyomon követésére, és minden alkalmazás frissítése szükséges.

Ismerje meg az operációs rendszer és a keretrendszert az ACR-feladatok harmadik oktatóanyagban javítása [automatizálása kép épül, amely az Azure Container Registry feladatok rendszerkép alapszintű frissítésének](container-registry-tutorial-base-image-update.md).

> [!NOTE]
> Alaplemezkép frissíti az eseményindítót buildek csak akkor lehetséges, ha mind az alap- és az alkalmazás-lemezképek az ugyanazon az Azure container registry található, vagy egy nyilvános Docker Hub adattárban található alap.

## <a name="multi-step-tasks-preview"></a>Többlépéses feladatok (előzetes verzió)

Több lépésből álló feladatokat, az ACR-feladatokat, egy előzetes funkció biztosít lépésközönként feladatdefiníció és végrehajtási létrehozásához, teszteléséhez és javítás tárolórendszerképeket a felhőben. A feladatlépések tárolólemezképek különálló buildelési és leküldéses műveleteit határozzák meg. Emellett egy vagy több tároló végrehajtását is definiálhatják; a lépések a tárolót használják végrehajtási környezetnek.

Például egy több lépésből álló feladat, amely automatizálja a következő hozhat létre:

1. A webes alkalmazás rendszerkép összeállítása
1. A webalkalmazás tárolójának futtatása
1. Egy alkalmazás webes teszt kép létrehozása
1. Futtassa a webes teszt alkalmazástárolók, szemben a futó teszteket hajt végre, amely alkalmazástárolók
1. Ha a vizsgálatok sikeresek, egy Helm-diagram archív csomag létrehozása
1. Hajtsa végre egy `helm upgrade` Helm-diagram archív új csomag telepítése

Többlépéses feladatok lehetővé teszik az épület, fut, és a egy kép több algyűjteményeinek összefüggő lépésekre lépés közötti függőségi támogatásával tesztelése felosztása. Az ACR-feladatokat több lépésből álló feladatokat a lemezkép létrehozása, tesztelése, és operációs rendszerrel és javítási munkafolyamatra keretrendszer részletesebb hozzáféréssel rendelkeznek.

További információ a több lépésből álló feladatokat [többlépéses buildelési, tesztelési és patch feladatok futtatása az ACR-feladatokban](container-registry-tasks-multi-step.md).

> [!IMPORTANT]
> ACR-feladatokat a többlépéses feladat funkció jelenleg előzetes verzióban érhető el. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. Ezt a képességet néhány szempontja változhat előtt általánosan (elérhetővé tétel GA)

## <a name="next-steps"></a>További lépések

Amikor elkészült, az operációs rendszer és a felhőben a tárolólemezképek létrehozásával javítás keretrendszer automatizálása, tekintse meg háromrészes ACR feladatok oktatóanyag-sorozat.

> [!div class="nextstepaction"]
> [Tárolórendszerképek összeállítása a felhőben az Azure Container Registry Tasks használatával](container-registry-tutorial-quick-task.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task]: /cli/azure/acr#az-acr-task
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli#log-in-with-a-service-principal

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png