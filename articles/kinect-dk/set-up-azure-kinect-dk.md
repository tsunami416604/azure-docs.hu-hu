---
title: Gyorsútmutató – Az Azure Kinect DK beállítása
description: Ez a gyorsútmutató az Azure Kinect DK-hardver beállításával kapcsolatos utasításokat tartalmaz
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: quickstart
ms.date: 02/12/2020
keywords: azure, kinect, fejlesztői készlet, azure dk, beállítás, hardver, gyors, usb, bekapcsolás, viewer, érzékelő, streamelés, beállít, SDK, belső vezérlőprogram
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: b33565f319de77385c966f742fa52391f4869eed
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211278"
---
# <a name="quickstart-set-up-your-azure-kinect-dk"></a>Gyorsútmutató: Az Azure Kinect DK beállítása

Ez a gyorsútmutató az Azure Kinect DK beállításával kapcsolatos utasításokat tartalmazza. Bemutatjuk, hogyan tesztelheti az érzékelőstream-vizualizációt és hogyan használhatja az [Azure Kinect Viewert](azure-kinect-viewer.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="system-requirements"></a>Rendszerkövetelmények

Tekintse meg a [Rendszerkövetelményeket](system-requirements.md) annak ellenőrzéséhez, hogy a gazdagép konfigurációja megfelel-e az Azure Kinect DK minimális követelményeinek.

## <a name="set-up-hardware"></a>A hardver beállítása

> [!NOTE]
> Az eszköz használata előtt mindenképpen távolítsa el a kamerán lévő védőfóliát.

1. Csatlakoztassa a tápkábelt az eszköz hátoldalán található megfelelő csatlakozóhoz. Csatlakoztassa az USB-adaptert a kábel másik végéhez, majd helyezze be az adaptert a hálózati csatlakozóaljzatba.
2. Csatlakoztassa az USB-adatkábelt az eszközhöz, majd a számítógép USB 3.0-portjához.
   >[!NOTE]
   >A legjobb eredmény eléréséhez közvetlenül az eszközhöz és a számítógéphez csatlakoztassa a kábelt. Kerülje a bővítők és külön adapterek használatát a csatlakoztatáskor.

3. Győződjön meg arról, hogy a bekapcsolt állapotot jelző LED (az USB-kábel mellett) folyamatos fehér színnel világít.
  
   Néhány másodpercet igénybe vesz, amíg bekapcsol az eszköz. Az eszköz akkor áll készen a használatra, amikor az elülső LED-es streamelési visszajelző kikapcsol.  

   A bekapcsolt állapotot jelző LED-ről további információt [a fényjelzések magyarázatát ismertető részben](hardware-specification.md#what-does-the-light-mean) talál

    ![Az összes eszközfunkció](./media/quickstarts/full-device-features.png)

## <a name="download-the-sdk"></a>Az SDK letöltése

1. Kattintson a hivatkozásra [az SDK letöltéséhez](sensor-sdk-download.md).
2. Telepítse az SDK-t a számítógépen.

## <a name="update-firmware"></a>A belső vezérlőprogram frissítése

Az SDK megfelelő működéséhez a belső vezérlőprogram legfrissebb verziójára van szükség. A belső vezérlőprogram verziójának ellenőrzéséhez és frissítéséhez kövesse [az Azure Kinect DK belsővezérlőprogram-frissítésével foglalkozó részben](update-device-firmware.md) megadott lépéseket.

## <a name="verify-that-the-device-streams-data"></a>Ellenőrizze, hogy az eszköz streamel-e adatokat

1. Indítsa el az [Azure Kinect Viewert](azure-kinect-viewer.md). Ezt az eszközt az alábbi módszerek valamelyikével indíthatja el:
   - A Viewert elindíthatja a parancssorból, vagy úgy is, ha duplán kattint a végrehajtható fájlra. A fájl (`k4aviewer.exe`) az SDK-eszközök könyvtárában található (például: `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe`, ahol az `X.Y.Z` az SDK telepített verzióját jelöli).
   - Az Azure Kinect Viewer az eszköz **Start** menüjéből is elindítható.
2. Az Azure Kinect Viewerben válassza az **Eszköz megnyitása** > **Start** lehetőséget.

    ![Azure Kinect Viewer](./media/quickstarts/viewer.png)

3. Ellenőrizze, hogy az eszköz minden érzékelőstreamet vizualizál-e:
   - Mélységi kamera
   - Színes kamera
   - Infravörös kamera
   - IMU
   - Mikrofonok

    ![Vizualizációs eszköz](./media/quickstarts/visualization-tool.png)

Végzett az Azure Kinect DK beállításával. Most már elkezdheti az alkalmazások fejlesztését vagy a szolgáltatások integrálását.

Ha problémát tapasztal, tekintse meg a [Hibaelhárítás](troubleshooting.md) részt.

## <a name="see-also"></a>Lásd még

- [Azure Kinect DK – Hardverinformációk](hardware-specification.md)
- [Az eszköz belső vezérlőprogramjának frissítése](update-device-firmware.md)
- További tudnivalók az [Azure Kinect Viewerről](azure-kinect-viewer.md)

## <a name="next-steps"></a>További lépések

Ha az Azure Kinect DK-t üzembe helyezte, megismerkedhet a következőkkel is:
> [!div class="nextstepaction"]
> [Érzékelőstreamek rögzítése fájlba](record-sensor-streams-file.md)
