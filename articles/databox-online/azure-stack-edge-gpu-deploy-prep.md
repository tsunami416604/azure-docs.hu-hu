---
title: Oktatóanyag a Azure Portal, Datacenter-környezet előkészítéséhez a Azure Stack Edge Pro GPU üzembe helyezéséhez | Microsoft Docs
description: Az Azure Stack Edge Pro GPU üzembe helyezésével kapcsolatos első oktatóanyag a Azure Portal előkészítését foglalja magában.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: e1cb4555b1eab930286e7a27988b3b372b109070
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570899"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-with-gpu"></a>Oktatóanyag: Felkészülés a Azure Stack Edge Pro és a GPU üzembe helyezésére 

Ez az első oktatóanyag az üzembe helyezési oktatóanyagok sorozatában, amelyek az Azure Stack Edge Pro és a GPU használatával történő teljes körű telepítéséhez szükségesek. Ez az oktatóanyag leírja, hogyan készítheti elő a Azure Portal egy Azure Stack Edge-erőforrás üzembe helyezéséhez.

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. A portál előkészítése kevesebb mint 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új erőforrás létrehozása
> * Az aktiválási kulcs lekérése

### <a name="get-started"></a>Bevezetés

A Azure Stack Edge Pro üzembe helyezéséhez először elő kell készítenie a környezetet. Ha a környezet elkészült, kövesse a szükséges lépéseket, és ha szükséges, az eszköz teljes telepítéséhez választható lépéseket és eljárásokat. A részletes telepítési utasítások jelzik, hogy mikor kell végrehajtania ezeket a szükséges és választható lépéseket.

| Lépés | Leírás |
| --- | --- |
| **Előkészítés** |Ezeket a lépéseket a közelgő üzembe helyezés előkészítésekor kell elvégezni. |
| **[Üzembe helyezési konfigurációs ellenőrzőlista](#deployment-configuration-checklist)** |Ezzel az ellenőrzőlistával információkat gyűjthet és rögzíthet az üzembe helyezés előtt és közben. |
| **[Központi telepítés előfeltételei](#prerequisites)** |Ezek alapján lehet ellenőrizni, hogy a környezet készen áll-e az üzembe helyezésre. |
|  | |
|**Üzembe helyezési oktatóanyagok** |Ezek az oktatóanyagok a Azure Stack Edge Pro-eszköz éles környezetben történő üzembe helyezéséhez szükségesek. |
|**[1. a Azure Portal előkészítése Azure Stack Edge Pro-hoz](azure-stack-edge-gpu-deploy-prep.md)** |Hozza létre és konfigurálja a Azure Stack Edge-erőforrást, mielőtt telepítené a Azure Stack Box Edge fizikai eszközét. |
|**[2. Telepítse a Azure Stack Edge Pro-t](azure-stack-edge-gpu-deploy-install.md)**|A Azure Stack Edge Pro fizikai eszköz kicsomagolása, állványa és kábele.  |
|**[3. kapcsolódás Azure Stack Edge Pro-hoz](azure-stack-edge-gpu-deploy-connect.md)** |Az eszköz telepítése után kapcsolódjon az eszköz helyi webes felhasználói felületéhez.  |
|**[4. a Azure Stack Edge Pro hálózati beállításainak konfigurálása](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)** |Konfigurálja a hálózatot, beleértve az eszköz számítási hálózatát és WebProxy-beállításait.   |
|**[5. az eszközbeállítások konfigurálása Azure Stack Edge Pro-hoz](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)** |Rendeljen hozzá egy eszköznév és egy DNS-tartományt, konfigurálja a frissítési kiszolgálót és az eszköz idejét. |
|**[6. az Azure Stack Edge Pro biztonsági beállításainak konfigurálása](azure-stack-edge-gpu-deploy-configure-certificates.md)** |Konfigurálja az eszköz tanúsítványait. Használja az eszköz által generált tanúsítványokat, vagy hozza létre saját tanúsítványait.   |
|**[7. a Azure Stack Edge Pro aktiválása](azure-stack-edge-gpu-deploy-activate.md)** |Az eszköz aktiválásához használja az aktiválási kulcsot a szolgáltatásból. Az eszköz készen áll az SMB-vagy NFS-megosztások beállítására, vagy a REST-en keresztüli kapcsolódásra. |
|**[8. a számítás konfigurálása](azure-stack-edge-gpu-deploy-configure-compute.md)** |Konfigurálja a számítási szerepkört az eszközön. Ez egy Kubernetes-fürtöt is létrehoz. |
|**[9a. Adatok átvitele peremhálózati megosztásokkal](azure-stack-edge-j-series-deploy-add-shares.md)** |Vegyen fel megosztásokat, és csatlakozzon a megosztásokhoz SMB vagy NFS használatával. |
|**[9b. Adatok átvitele peremhálózati Storage-fiókokkal](azure-stack-edge-j-series-deploy-add-storage-accounts.md)** |Adja hozzá a Storage-fiókokat, és kapcsolódjon a blob Storage-hoz REST API-kon keresztül. |


Most már megkezdheti az Azure Stack Edge Pro-eszközhöz tartozó szoftver konfigurációjával kapcsolatos információk összegyűjtését.

## <a name="deployment-configuration-checklist"></a>Üzembehelyezési konfigurációs ellenőrzőlista

Az eszköz üzembe helyezése előtt össze kell gyűjtenie az adatokat a Azure Stack Edge Pro-eszközön lévő szoftver konfigurálásához. Ezen információk némelyikének előzetes előkészítése megkönnyíti az eszköz telepítésének folyamatát a környezetben. Az eszköz üzembe helyezése során jegyezze fel a konfiguráció részleteit az [Azure stack Edge Pro üzembehelyezési konfigurációjának ellenőrzőlistája](azure-stack-edge-gpu-deploy-checklist.md) segítségével.


## <a name="prerequisites"></a>Előfeltételek

A következő konfigurációs előfeltételek vonatkoznak az Azure Stack Edge-erőforrásra, az Azure Stack Edge Pro-eszközre és az adatközpont-hálózatra.

### <a name="for-the-azure-stack-edge-resource"></a>Az Azure Stack Edge-erőforráshoz

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- A Microsoft Azure-előfizetés engedélyezve van egy Azure Stack Edge-erőforráshoz. Győződjön meg arról, hogy olyan támogatott előfizetést használt, mint például a [Microsoft nagyvállalati szerződés (EA)](https://azure.microsoft.com/overview/sales-number/), a [Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)vagy a [Microsoft Azure szponzorálás](https://azure.microsoft.com/offers/ms-azr-0036p/). A használatalapú fizetéses előfizetések nem támogatottak. Az Azure-előfizetés típusának azonosításához tekintse meg a [Mi az az Azure-ajánlat?](../cost-management-billing/manage/switch-azure-offer.md#what-is-an-azure-offer)című témakört.
- Az Azure Stack Edge Pro/Data Box Gateway, a IoT Hub és az Azure Storage-erőforrások esetében tulajdonosi vagy közreműködői hozzáférése van az erőforráscsoport szintjén.

    - Ha Azure Stack Edge/Data Box Gateway erőforrást szeretne létrehozni, akkor az erőforrás-csoport szintjén a közreműködő (vagy magasabb szintű) jogosultsággal kell rendelkeznie. 
    - Győződjön meg arról is, hogy a `Microsoft.DataBoxEdge` szolgáltató regisztrálva van. Bármely IoT Hub erőforrás létrehozásához regisztrálni kell a `Microsoft.Devices` szolgáltatót. 
        - Erőforrás-szolgáltató regisztrálásához a Azure Portal nyissa meg a **Home >-Előfizetések > az előfizetéses > erőforrás-szolgáltatókat**. 
        - Keresse meg `Microsoft.DataBoxEdge` és regisztrálja az erőforrás-szolgáltatót. 
    - A Storage-fiók erőforrásának létrehozásához ismét közreműködői vagy magasabb szintű hozzáférési hatókörre van szükség az erőforráscsoport szintjén. Az Azure Storage alapértelmezés szerint regisztrált erőforrás-szolgáltató.
- Rendszergazdai vagy felhasználói hozzáféréssel rendelkezik Azure Active Directory Graph API az aktiválási kulcs vagy a hitelesítő adatok (például a Storage-fiókot használó megosztás létrehozása) létrehozásához. További információ: [Azure Active Directory Graph API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).


### <a name="for-the-azure-stack-edge-pro-device"></a>Az Azure Stack Edge Pro-eszközhöz

Fizikai eszköz üzembe helyezése előtt győződjön meg az alábbiakról:

- Áttekintette a szállítási csomagban található biztonsági információkat.
- Az eszköz csatlakoztatásához az adatközpontban egy szabványos 19. állványban érhető el egy 1U-tárolóhely.
- Olyan sima, stabil és szintű munkafelülethez férhet hozzá, ahol az eszköz biztonságosan megnyugodtan működik.
- A hely, ahol be kívánja állítani az eszközt, szabványos AC-teljesítménnyel rendelkezik egy független forrásból, vagy egy szünetmentes áramforrással (UPS) rendelkező rack Power Distribution Unit (PDU).
- Hozzáférése kell rendelkeznie egy fizikai eszközhöz.


### <a name="for-the-datacenter-network"></a>Adatközponti hálózat

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Az adatközpontban található hálózat a Azure Stack Edge Pro-eszköz hálózati követelményeinek megfelelően van konfigurálva. További információ: [Azure stack Edge Pro rendszerkövetelményei](azure-stack-edge-system-requirements.md).

- A Azure Stack Edge Pro normál működési feltételei:

    - Legalább 10 MB/s letöltési sávszélesség, hogy az eszköz naprakész maradjon.
    - A fájlok átviteléhez legalább 20 Mbps dedikált feltöltési és letöltési sávszélesség szükséges.

## <a name="create-a-new-resource"></a>Új erőforrás létrehozása

Ha rendelkezik meglévő Azure Stack Edge-erőforrással a fizikai eszköz kezeléséhez, hagyja ki ezt a lépést, és lépjen [az aktiválási kulcs lekérése](#get-the-activation-key)elemre.

Azure Stack peremhálózati erőforrás létrehozásához hajtsa végre a következő lépéseket a Azure Portal.

1. A Microsoft Azure hitelesítő adataival jelentkezzen be a Azure Portalba ezen az URL-címen: [https://portal.azure.com](https://portal.azure.com) .

2. A bal oldali panelen válassza az **+ erőforrás létrehozása**lehetőséget. Keresse meg és válassza ki **Azure stack Edge/Data Box Gateway**. Kattintson a **Létrehozás** gombra. 

3. Válassza ki az Azure Stack Edge Pro-eszközhöz használni kívánt előfizetést. Válassza ki azt az országot, ahová a fizikai eszközt el szeretné szállítani. Válassza az **eszközök megjelenítése**lehetőséget.

    ![1. erőforrás létrehozása](media/azure-stack-edge-gpu-deploy-prep/create-resource-1.png)

4. Válassza az eszköz típusa lehetőséget. A **Azure stack Edge Pro**területen válassza **a Azure stack Edge Pro GPU-val** lehetőséget, majd válassza a **kiválasztás**lehetőséget. Ha bármilyen probléma merül fel, vagy nem tudja kijelölni az eszköz típusát, ugorjon a [rendezési problémák elhárítása](azure-stack-edge-troubleshoot-ordering.md)című témakörre.

    ![3. erőforrás létrehozása](media/azure-stack-edge-gpu-deploy-prep/create-resource-3.png)

5. Az üzleti igények alapján kiválaszthatja, hogy az NVIDIA-ból 1 vagy 2 grafikus processzorral (GPU) Azure Stack Edge Pro. 

    ![4. erőforrás létrehozása](media/azure-stack-edge-gpu-deploy-prep/create-resource-4.png)

6. Az **alapvető beállítások** lapon adja meg vagy válassza ki a következő **projekt részleteit**.
    
    |Beállítás  |Érték  |
    |---------|---------|
    |Előfizetés    |Ezt a rendszer automatikusan kitölti a korábbi kiválasztás alapján. A számlázási fiókhoz társított előfizetés. |
    |Erőforráscsoport  |Válasszon ki egy meglévő csoportot, vagy hozzon létre egy újat.<br>Itt tekinthet meg további információt az [Azure-erőforráscsoportokkal](../azure-resource-manager/resource-group-overview.md) kapcsolatban.     |

7. Adja meg vagy válassza ki a következő **példány részleteit**.

    |Beállítás  |Érték  |
    |---------|---------|
    |Név   | Az erőforrást azonosító valódi név.<br>A névnek 2–50 karakter hosszúságúnak kell lennie, és csak betűket, számokat, illetve kötőjelet tartalmazhat.<br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.        |
    |Region     |Az Azure Stack Edge-erőforrást tartalmazó régiók listáját itt tekintheti meg: [régiónként elérhető Azure-termékek](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Government használata esetén az összes kormányzati régió elérhető az [Azure-régiókban](https://azure.microsoft.com/global-infrastructure/regions/)látható módon.<br> Az eszköz üzembe helyezésének földrajzi régiójához legközelebb eső helyet válasszon.|

    ![5. erőforrás létrehozása](media/azure-stack-edge-gpu-deploy-prep/create-resource-5.png)


8. Válassza a **Next (tovább): szállítási címet**.

    - Ha már van eszköz, válassza az **Azure stack Edge Pro-eszközhöz**tartozó kombinált listát.

        ![6. erőforrás létrehozása](media/azure-stack-edge-gpu-deploy-prep/create-resource-6.png)

    - Ha ez az új eszköz, amelyet Ön megrendelt, adja meg a kapcsolattartó nevét, a vállalatot, az eszköz szállítását és a kapcsolattartási adatokat.

        ![7. erőforrás létrehozása](media/azure-stack-edge-gpu-deploy-prep/create-resource-7.png)

9. Kattintson a **Tovább gombra: címkék**. Opcionálisan megadhat címkéket az erőforrások kategorizálása és a számlázás konszolidálása érdekében. Válassza a **Tovább: Ellenőrzés és létrehozás** lehetőségre.

10. A **felülvizsgálat + létrehozás** lapon tekintse át a **díjszabás részleteit**, **használati feltételek**és az erőforrás részleteit. Válassza ki az **adatvédelmi feltételeket áttekintő**kombinált listát.

    ![8. erőforrás létrehozása](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

11. Kattintson a **Létrehozás** gombra.

Az erőforrás létrehozása néhány percet vesz igénybe. Az erőforrás sikeres létrehozása és üzembe helyezése után értesítést kap. Válassza az **Erőforrás megnyitása** lehetőséget.

![Ugrás az Azure Stack Edge Pro-erőforrásra](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-1.png)

A megrendelés elhelyezése után a Microsoft áttekinti a rendelést, és elküldi Önt (e-mailben) a szállítási adatokkal.

<!--![Notification for review of the Azure Stack Edge Pro order](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png)-->

Ha a rendelési folyamat során problémákba ütközik, tekintse meg a [rendelési problémák elhárítása](azure-stack-edge-troubleshoot-ordering.md)című témakört.

## <a name="get-the-activation-key"></a>Az aktiválási kulcs lekérése

Az Azure Stack Edge-erőforrás működésének megkezdése után le kell kérnie az aktiválási kulcsot. Ezzel a kulccsal aktiválhatja és összekapcsolhatja Azure Stack Edge Pro-eszközét az erőforrással. Ezt a kulcsot lekérheti most, amíg az Azure Portalon van.

1. Válassza ki a létrehozott erőforrást. Válassza az **Áttekintés** lehetőséget, majd válassza az **eszköz beállítása**lehetőséget.

    ![Eszköz beállításának kiválasztása](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png)

2. A csempe **aktiválása** lapon válassza a **kulcs létrehozása** lehetőséget az aktiválási kulcs létrehozásához. Kattintson a másolás ikonra a kulcs másolásához és a későbbi használatra mentéséhez.

    ![Aktiválási kulcs lekérése](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

> [!IMPORTANT]
> - Az aktiválási kulcs három nappal a létrehozása után lejár.
> - Ha a kulcs lejárt, állítson be egy új kulcsot. A régebbi kulcs nem lesz érvényes.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte Azure Stack Edge Pro-témaköröket, például a következőket:

> [!div class="checklist"]
> * Új erőforrás létrehozása
> * Az aktiválási kulcs lekérése

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan telepítheti Azure Stack Edge Pro-t.

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro telepítése](./azure-stack-edge-gpu-deploy-install.md)



