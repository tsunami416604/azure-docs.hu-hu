---
title: "Egy Azure IoT Hub eszköz kiépítése szolgáltatáshoz eszközt kiépítése |} Microsoft Docs"
description: "Az eszköz használata az Azure IoT Hub eszköz kiépítése szolgáltatás egyetlen IoT-központ telepítéséhez"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: bf50699d2dc67294d554ba15713254a8b88d8ade
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Biztosítsa az eszköz használata az Azure IoT Hub eszköz kiépítése szolgáltatás IoT hubhoz

Az előző oktatóanyag megtudta, hogyan eszköz beállítása az eszköz kiépítése szolgáltatáshoz való kapcsolódáshoz. Ebből az oktatóanyagból megtudhatja, hogyan a szolgáltatás használatához az eszközt egy IoT-központ telepítéséhez használja  **_beléptetési listák_**. Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Regisztrálja az eszközt
> * Indítsa el az eszközt
> * Ellenőrizze, hogy az eszköz regisztrálva van

## <a name="prerequisites"></a>Előfeltételek

Mielőtt továbblépne, győződjön meg arról, hogy az eszköz konfigurálása és a *hardveres biztonsági modul* lásd az oktatóanyag a [eszköz beállítása az Azure IoT Hub eszköz kiépítése szolgáltatáshoz használatával kiépítéséhez](./tutorial-set-up-device.md).


<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>Regisztrálja az eszközt

Ezt a lépést foglal magában, az eszköz egyedi biztonsági összetevők hozzáadása az kiépítése szolgáltatáshoz. Ezen biztonsági összetevők a következők:

- TPM-alapú eszközök esetén:
    - A *ellenőrzőkulccsal* egyedi minden TPM lapka vagy szimulátor. Olvassa el a [megértéséhez TPM ellenőrzőkulccsal](https://technet.microsoft.com/library/cc770443.aspx) további információt.
    - A *regisztrációs Azonosítót* névtér/hatókörében eszköz egyedi azonosítására szolgál. Ez lehet, vagy nem lehet ugyanaz, mint az eszközazonosító. Az azonosító megadása kötelező, minden eszközhöz. A TPM-alapú eszközök esetén a regisztrációs Azonosítót a platformmegbízhatósági modul, például egy SHA-256 kivonatoló, a TPM ellenőrzőkulcs kell származnia.

    ![Regisztrálási információ TPM a portálon](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)

- X.509-alapú eszközök esetén:
    - A [az X.509 tanúsítvánnyal](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx) lapka használata vagy a szimuláció, formájában, vagy egy *.pem* vagy egy *.cer* fájlt. Az egyes beléptetési kell használnia a *aláíró tanúsítvány* a X.509 rendszerhez, a beléptetési csoportok kell használnia a *legfelső szintű tanúsítvány*.

    ![X.509 regisztrációs adatait a portálon](./media/tutorial-provision-device-to-hub/x509-device-enrollment.png)


Regisztrálja az eszközt, az eszköz kiépítése szolgáltatáshoz két módja van:

- **Beléptetési csoportok** , amelyek egy adott tanúsítvány mechanizmus egy eszközcsoportra jelképez. Azt javasoljuk, egy beléptetési csoport az eszközök, amelyek kezdeti szükségeskonfiguráció megosztásához sok vagy eszközök valamennyi amelyet ugyanannak a bérlőnek.

    ![A portál X.509 beléptetési csoportok](./media/tutorial-provision-device-to-hub/x509-enrollment-groups.png)

- **Egyes regisztrációkat** egyetlen eszközt, amely előfordulhat, hogy regisztrálja az eszköz kiépítése szolgáltatáshoz tartozó bejegyzés jelképez. Egyes regisztrációkat használhatja bármelyik x509 tanúsítványokat vagy SAS jogkivonatok (a valós vagy virtuális TPM), állapotigazolási mechanizmusok. Azt javasoljuk, egyes regisztrációkat az eszközök, amelyek külön kezdeti konfigurációt igényelnek, vagy az eszközök, amelyek is csak TPM vagy virtuális TPM SAS-tokenje igazoló mechanizmusként. Előfordulhat, hogy az egyes regisztrációkat a kívánt IoT hub eszköz-azonosító van megadva.

Az eszközt a portál regisztrálás lépéseit a következők:

1. Megjegyzés: a biztonsági összetevők a hardveres biztonsági MODULT, az eszközön. Szükség lehet a című részben leírt API-k [bontsa ki a biztonsági összetevők](./tutorial-set-up-device.md#extractsecurity) az előző oktatóanyag fejlesztői környezetben.

1. Jelentkezzen be az Azure Portalra, a bal oldali menüben kattintson az **Összes erőforrás** gombra, és nyissa meg az eszközkiépítési szolgáltatást.

1. Az eszközkiépítési szolgáltatás összefoglalás panelén válassza a **Beléptetések kezelése** lehetőséget. Válassza **egyedi regisztrációkat** lapon vagy a **beléptetési csoportok** lapon az eszköz beállításának megfelelően. Kattintson a **Hozzáadás** gombra az oldal tetején. Válassza ki **TPM** vagy **X.509** , a identitását igazoló *mechanizmus*, és adja meg a megfelelő biztonsági összetevők a korábban tárgyalt. Megadhat egy új **IoT-központ Eszközazonosító**. Ha végzett, kattintson a **Mentés** gombra. 

1. Ha sikeresen regisztrálta az eszközét, kell megjelennie, megjelenik a portálon, mint a következőket:

    ![A portál sikeres TPM-regisztráció](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)


## <a name="start-the-device"></a>Indítsa el az eszközt

Ezen a ponton a következő telepítő készen áll a eszközregisztráció:

1. Az eszköz kiépítése szolgáltatáshoz regisztrált az eszközt vagy eszközöket és 
2. A konfigurált és az eszköz kiépítése szolgáltatáshoz ügyfél SDK használata alkalmazás keresztül érhető el a HSM lapka készen áll az eszközén.

Indítsa el az eszközöket, hogy az ügyfélalkalmazás számára az eszköz kiépítése szolgáltatáshoz a regisztrációs elindításához.  


## <a name="verify-the-device-is-registered"></a>Ellenőrizze, hogy az eszköz regisztrálva van

Egyszer az eszköz indítása, a következő műveleteket kell végrehajtani. Tekintse meg a TPM-szimulátor mintaalkalmazás [dps_client_sample](https://github.com/Azure/azure-iot-device-auth/blob/master/dps_client/samples/dps_client_sample/dps_client_sample.c) további részleteket. 

1. Az eszköz regisztrációs kérelmet küld az eszköz kiépítése szolgáltatáshoz.
2. TPM-eszközök esetén az eszköz kiépítése szolgáltatáshoz küld vissza a regisztrációs kihívás, amelyhez az eszköz válaszol. 
3. A sikeres regisztráció az eszköz kiépítése szolgáltatáshoz elküldi az eszközt az IoT hub URI, eszköz-Azonosítóját és a titkosított kulcs biztonsági. 
4. Az IoT-központ ügyfélalkalmazás az eszköz ezután csatlakozik a központ. 
5. A sikeres kapcsolat szeretne az elosztóhoz, kell megjelennie az eszköz jelennek meg az IoT-központ **eszköz Explorer**. 

    ![Sikeres kapcsolatot a portálon](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Regisztrálja az eszközt
> * Indítsa el az eszközt
> * Ellenőrizze, hogy az eszköz regisztrálva van

A következő oktatóanyag áttekintésével megismerheti, hogyan több különböző terhelésű hubok központilag továbblépés. 

> [!div class="nextstepaction"]
> [Különböző terhelésű IoT-központok eszközök beállításához](./tutorial-provision-multiple-hubs.md)
