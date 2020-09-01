---
title: Oktatóanyag a Azure Stack Edge-eszköz tanúsítványainak konfigurálásához a GPU-val Azure Portalban | Microsoft Docs
description: Az Azure Stack Edge üzembe helyezésére vonatkozó oktatóanyag arra utasítja Önt, hogy konfigurálja a tanúsítványokat a fizikai eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: fcaac4c1efe6507d4f69acd897e456400b1f61f1
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89087887"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-with-gpu"></a>Oktatóanyag: tanúsítványok konfigurálása a Azure Stack Edge-hez GPU-val

Ez az oktatóanyag azt ismerteti, hogyan konfigurálhatja a Azure Stack Edge-eszközhöz tartozó tanúsítványokat a helyi webes felhasználói felület használatával.

Az ehhez a lépéshez szükséges idő eltérő lehet attól függően, hogy a rendszer milyen módon hozza meg a tanúsítvány folyamatát az adott környezetben. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Tanúsítványok konfigurálása a fizikai eszközhöz

## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack Edge-eszköz GPU-val való konfigurálása és beállítása előtt győződjön meg az alábbiakról:

* A fizikai eszközt a [Azure stack Edge telepítése](azure-stack-edge-gpu-deploy-install.md)című részletesen telepítette.
* Ha azt tervezi, hogy saját tanúsítványokat hoz:
    - A tanúsítványokat a megfelelő formátumban kell megadni, beleértve az aláíró lánc tanúsítványát.
    - Ha az eszköz üzembe helyezése Azure Government vagy Azure Government Secret vagy Azure Government legfelső szintű titkos felhőben történik, és nem az Azure nyilvános felhőben van telepítve, akkor az eszköz aktiválása előtt regisztrálni kell egy aláíró láncot. 
    A tanúsítvány részleteiért lépjen a [tanúsítványok kezelése](azure-stack-edge-j-series-manage-certificates.md)elemre.


## <a name="configure-certificates-for-device"></a>Tanúsítványok konfigurálása az eszközhöz


1. A **Biztonság** csempén válassza a tanúsítványok **konfigurálása** lehetőséget. 

    ![Helyi webes felhasználói felület "tanúsítványok" lapja](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)

2. Attól függően, hogy módosította-e az eszköz nevét vagy a DNS-tartományt az **eszközbeállítások** csempén, az alábbi lehetőségek közül választhat a tanúsítványok számára.

    - Ha nem módosította az eszköz nevét vagy a DNS-tartományt a korábbi lépésben, és nem kívánja saját tanúsítványokat használni, akkor kihagyhatja ezt a lépést, és folytathatja a következő lépéssel. Az eszköz automatikusan létrehozta az önaláírt tanúsítványokat. 

        ![Helyi webes felhasználói felület "tanúsítványok" lapja](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - Ha módosította az eszköz nevét vagy a DNS-tartományt, az eszköz sikeres aktiválásához az alábbi lehetőségek közül választhat: 
    
        - Az összes eszköz tanúsítványának előállítása. **Az eszköz tanúsítványait csak tesztelésre kell használni, és nem használhatók éles számítási feladatokhoz.** További információért látogasson el az [eszközök tanúsítványainak Előállítása Azure stack szélén](#generate-device-certificates).

        - Saját tanúsítványok használata. Saját aláírt végponti tanúsítványokat és a hozzájuk tartozó aláíró láncokat is használhatja. Először adja hozzá az aláíró láncot, majd töltse fel a végponti tanúsítványokat. **Javasoljuk, hogy mindig saját tanúsítványokat használjon az éles számítási feladatokhoz.** További információ: [saját tanúsítványok használata az Azure stack Edge-eszközön](#bring-your-own-certificates).
    
        - Saját tanúsítványokat is létrehozhat, és létrehozhat néhány eszköz tanúsítványát. A **tanúsítványok előállítása** lehetőség csak az eszköz tanúsítványait hozza újra.

    - Ha módosította az eszköz nevét vagy a DNS-tartományt, és nem állít elő tanúsítványokat, vagy saját tanúsítványokat hoz, akkor az aktiválás le lesz tiltva.


### <a name="generate-device-certificates"></a>Eszközök tanúsítványainak előállítása

Az eszközök tanúsítványainak létrehozásához kövesse az alábbi lépéseket.

Az Azure Stack Edge-eszközök tanúsítványainak újbóli létrehozásához és letöltéséhez kövesse az alábbi lépéseket:

1. Az eszköz helyi felhasználói felületén lépjen a **Configuration > tanúsítványok**elemre. Válassza a **tanúsítványok előállítása**lehetőséget.

    ![Tanúsítvány előállítása és letöltése 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. Az **eszközök tanúsítványainak előállítása**lapon válassza a **készítés**lehetőséget.

    ![A 2. tanúsítvány előállítása és letöltése](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    Az eszközök tanúsítványainak létrehozása és alkalmazása megtörtént. 
    
    > [!IMPORTANT]
    > Amíg a tanúsítvány-létrehozási művelet folyamatban van, ne hozza létre a saját tanúsítványait, és próbálja meg hozzáadni ezeket a **+ tanúsítvány hozzáadása** lehetőség használatával.

    Értesítést kap, ha a művelet sikeresen befejeződött. A lehetséges gyorsítótár-problémák elkerüléséhez indítsa újra a böngészőt. 
    
    ![Tanúsítvány készítése és letöltése 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. A **tanúsítványok** lapon megjelenik a **letöltési** oszlop, és az újragenerált tanúsítványok letöltésére mutató hivatkozások érhetők el. 

    ![Tanúsítvány készítése és letöltése 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

4. Válassza ki a tanúsítvány letöltési hivatkozását, és amikor a rendszer kéri, mentse a tanúsítványt. 

    ![Tanúsítvány készítése és letöltése 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. Ismételje meg a folyamatot a letölteni kívánt összes tanúsítvány esetében. 
    
    ![A 7. tanúsítvány előállítása és letöltése](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    Az eszköz által generált tanúsítványok a következő formátumú DER-tanúsítványként lesznek mentve: 

    `<Device name>_<Endpoint name>.cer`. Ezek a tanúsítványok az eszközre telepített megfelelő tanúsítványok nyilvános kulcsát tartalmazzák. 

Ezeket a tanúsítványokat telepítenie kell arra az ügyfél-rendszerre, amelyet a szolgáltatói eszközön lévő végpontokhoz való hozzáféréshez használ. Ezek a tanúsítványok megbízhatósági kapcsolatot létesít az ügyfél és az eszköz között.

A tanúsítványok importálásához és telepítéséhez azon az ügyfélen, amelyet az eszköz eléréséhez használ, kövesse a [tanúsítványok importálása az Azure stack Edge-eszközt elérő ügyfeleken](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)című témakör lépéseit. 

Azure Storage Explorer használata esetén PEM formátumban kell telepítenie a tanúsítványokat az ügyfélre, és az eszköz által létrehozott tanúsítványokat PEM formátumba kell alakítania. 

> [!IMPORTANT]
> - A letöltési hivatkozás csak az eszköz által generált tanúsítványokhoz érhető el, és nem, ha saját tanúsítványokat hoz létre.
> - Dönthet úgy, hogy az eszköz által generált tanúsítványokat kombinálja, és saját tanúsítványokat használ, amíg a többi tanúsítványra vonatkozó követelmény teljesül. További információért lépjen a [tanúsítványokra vonatkozó követelményeknél](azure-stack-edge-j-series-certificate-requirements.md).
    

### <a name="bring-your-own-certificates"></a>Saját tanúsítványok használata

A következő lépésekkel adhatja hozzá saját tanúsítványait, beleértve az aláíró láncot.

1. A tanúsítvány feltöltéséhez a **tanúsítvány** lapon válassza a **+ tanúsítvány hozzáadása**elemet.

    ![Helyi webes felhasználói felület "tanúsítványok" lapja](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. Először töltse fel az aláíró láncot, és válassza az **érvényesítés & Hozzáadás**lehetőséget.

    ![Helyi webes felhasználói felület "tanúsítványok" lapja](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. Most már tölthet fel más tanúsítványokat. Feltöltheti például a Azure Resource Manager és a blob Storage-végpont tanúsítványait.

    ![Helyi webes felhasználói felület "tanúsítványok" lapja](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    Fel is töltheti a helyi webes felhasználói felület tanúsítványát. A tanúsítvány feltöltése után el kell indítania a böngészőt, és törölnie kell a gyorsítótárat. Ezután csatlakoznia kell az eszköz helyi webes felhasználói felületéhez.  

    ![Helyi webes felhasználói felület "tanúsítványok" lapja](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    Fel is töltheti a csomópont-tanúsítványt.

    ![Helyi webes felhasználói felület "tanúsítványok" lapja](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    A tanúsítvány oldalának frissítenie kell, hogy tükrözze az újonnan hozzáadott tanúsítványokat.

    ![Helyi webes felhasználói felület "tanúsítványok" lapja](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)  

    > [!NOTE]
    > Az Azure nyilvános felhő kivételével az összes felhőalapú konfiguráció (Azure Government vagy Azure Stack) aktiválása előtt be kell állítani az aláírási lánc tanúsítványait.


Az eszköz már készen áll az aktiválásra.


## <a name="next-steps"></a>Következő lépések

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Tanúsítványok konfigurálása a fizikai eszközhöz

Az Azure Stack Edge-eszköz aktiválásához a következő témakörben talál további információt:

> [!div class="nextstepaction"]
> [Azure Stack Edge-eszköz aktiválása](./azure-stack-edge-gpu-deploy-activate.md)
