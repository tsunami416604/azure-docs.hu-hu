---
title: Az Azure Data Box Gateway eszköz hozzáférési, táp- és csatlakoztatási módja
description: Bemutatja, hogyan kezelhetők a hozzáférés, a tápellátás és a kapcsolódási mód az Azure Data Box Gateway eszközhöz, amely segít az adatok azure-ba való átvitelének
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: e4d85bd460c39964c9f42ac946e3522f5f129c1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474441"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Hozzáférés, tápellátás és kapcsolati mód kezelése az Azure Data Box Gateway számára

Ez a cikk ismerteti, hogyan kezelheti a hozzáférési, tápellátás átkapcsolási és kapcsolódási módot az Azure Data Box Gateway. Ezek a műveletek a helyi webes felhasználói felületen vagy az Azure Portalon keresztül hajtják végre.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Eszközhozzáférés kezelése
> * Kapcsolódási mód kezelése
> * Energiakezelése

## <a name="manage-device-access"></a>Eszközhozzáférés kezelése

A Data Box Gateway eszközhöz való hozzáférést egy eszközjelszó szabályozza. A jelszót a helyi internetes felhasználói felületen módosíthatja. Az eszköz jelszavát is alaphelyzetbe állíthatja az Azure Portalon.

### <a name="change-device-password"></a>Az eszköz jelszavának módosítása

Az eszköz jelszavának módosításához kövesse a helyi felhasználói felület alábbi lépéseit.

1. A helyi webes felhasználói felületen nyissa meg **a Karbantartás > jelszómódosítás című**részt.
2. Írja be az aktuális jelszót, majd az új jelszót. A megadott jelszónak 8 és 16 karakter között kell lennie. A jelszónak a következő karakterek közül 3-at kell tartalmaznia: nagybetűs, kisbetűs, numerikus és speciális karakterek. Erősítse meg az új jelszót.

    ![Change password](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Kattintson **a Jelszó módosítása gombra.**
 
### <a name="reset-device-password"></a>Eszközjelszavának alaphelyzetbe állítása

A visszaállítási munkafolyamat nem követeli meg a felhasználótól a régi jelszó visszahívását, és akkor hasznos, ha a jelszó elvész. Ez a munkafolyamat az Azure Portalon történik.

1. Az Azure Portalon nyissa **meg az Áttekintés > rendszergazdai jelszó alaphelyzetbe állítása című témakört.**

    ![Új jelszó létrehozása](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Írja be az új jelszót, majd erősítse meg. A megadott jelszónak 8 és 16 karakter között kell lennie. A jelszónak a következő karakterek közül 3-at kell tartalmaznia: nagybetűs, kisbetűs, numerikus és speciális karakterek. Kattintson **az Alaphelyzet gombra.**

    ![Új jelszó létrehozása](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Erőforrás-hozzáférés kezelése

A Data Box Edge/Data Box Gateway, az IoT Hub és az Azure Storage-erőforrás létrehozásához közreműködőként vagy magasabb szintű erőforráscsoport-szintű engedélyekre van szüksége. A megfelelő erőforrás-szolgáltatók regisztrálása is szükséges. Az aktiválási kulcsot és hitelesítő adatokat tartalmazó műveletekhez az Azure Active Directory Graph API-hoz való engedélyekis szükségesek. Ezeket a következő szakaszok ismertetik.

### <a name="manage-microsoft-graph-api-permissions"></a>A Microsoft Graph API-engedélyeinek kezelése

A Data Box Edge-eszköz aktiválási kulcsának létrehozásakor vagy a hitelesítő adatokat igénylő műveletek végrehajtásakor a Microsoft Graph API-hoz engedélyekre van szüksége. A hitelesítő adatokat igénylő műveletek a következők lehetnek:

-  Megosztás létrehozása társított tárfiókkal.
-  Olyan felhasználó létrehozása, aki hozzáférhet az eszközön lévő megosztásokhoz.

Hozzáféréssel kell `User` rendelkeznie az Active Directory-bérlőhöz, ahogy ahhoz `Read all directory objects`szükséges. Nem lehet vendégfelhasználó, mert nincs engedélye a `Read all directory objects`alkalmazáshoz. Ha Ön vendég, akkor az aktiválási kulcs létrehozása, a megosztás létrehozása a Data Box Edge-eszközön, a felhasználó létrehozása és az ehhez hasonló műveletek sikertelenek lesznek.

A Microsoft Graph API-hoz való hozzáférés biztosításáról a [Microsoft Graph engedélyek hivatkozása](https://docs.microsoft.com/graph/permissions-reference)című témakörben talál további információt.

### <a name="register-resource-providers"></a>Erőforrás-szolgáltatók regisztrálása

Egy erőforrás kiépítése az Azure-ban (az Azure Resource Manager modellben), szüksége van egy erőforrás-szolgáltató, amely támogatja az erőforrás létrehozását. Például egy virtuális gép kiépítése, rendelkeznie kell egy "Microsoft.Compute" erőforrás-szolgáltató érhető el az előfizetésben.
 
Az erőforrás-szolgáltatók az előfizetés-szinten vannak regisztrálva. Alapértelmezés szerint minden új Azure-előfizetés előre regisztrálva valamelyik gyakran használt erőforrás-szolgáltatónál. A "Microsoft.DataBoxEdge" erőforrás-szolgáltatója nem szerepel ebben a listában.

Nem kell hozzáférési engedélyeket adnia az előfizetési szinthez ahhoz, hogy a felhasználók olyan erőforrásokat hozhassanak létre, mint a "Microsoft.DataBoxEdge" az erőforráscsoportokon belül, amelyektulajdonosi jogokkal rendelkeznek, feltéve, hogy az erőforrások erőforrás-szolgáltatói már rendelkeznek Regisztrált.

Mielőtt megpróbálna létrehozni bármilyen erőforrást, győződjön meg arról, hogy az erőforrás-szolgáltató regisztrálva van az előfizetésben. Ha az erőforrás-szolgáltató nincs regisztrálva, meg kell győződnie arról, hogy az új erőforrást létrehozó felhasználó elegendő jogosultságkal rendelkezik a szükséges erőforrás-szolgáltató regisztrálásához az előfizetés szintjén. Ha még nem tette meg ezt is, akkor a következő hibaüzenet jelenik meg:

*Az \<előfizetés neve> nem rendelkezik az erőforrás-szolgáltató(k) regisztrálásához szükséges engedélyekkel: Microsoft.DataBoxEdge.*


Az aktuális előfizetésregisztrált erőforrás-szolgáltatóinak listájának levételéhez futtassa a következő parancsot:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

A Data Box `Microsoft.DataBoxEdge` Edge eszköz, regisztrálni kell. A `Microsoft.DataBoxEdge`regisztrációhoz az előfizetés rendszergazdájának a következő parancsot kell futtatnia:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Az erőforrás-szolgáltató regisztrálásáról az [Erőforrás-szolgáltató regisztrációjának hibáinak megoldása című](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors)témakörben talál további információt.

## <a name="manage-connectivity-mode"></a>Kapcsolódási mód kezelése

Az alapértelmezett normál módon kívül az eszköz részlegesen leválasztott vagy leválasztott módban is futhat. E módok mindegyikét az alábbiak szerint ismertetjük:

- **Részlegesen leválasztott** – Ebben a módban az eszköz nem tud adatokat feltölteni a megosztások azonban az Azure Portalon keresztül kezelhető.

    Ez a mód általában akkor használatos, ha forgalmi díjas műholdas hálózaton van, és a cél a hálózati sávszélesség-felhasználás minimalizálása. Az eszközfigyelési műveletek nél továbbra is előfordulhat minimális hálózati felhasználás.

- **Leválasztva** – Ebben a módban az eszköz teljesen le van választva a felhőről, és mind a felhőbeli feltöltések, mind a letöltések le vannak tiltva. Az eszköz csak a helyi webes felhasználói felületen keresztül kezelhető.

    Ezt a módot általában akkor használja, ha az eszközt offline állapotba szeretné helyezni.

Az eszközmód módosításához hajtsa végre az alábbi lépéseket:

1. Az eszköz helyi webes felhasználói felületén válassza a **Konfigurációs > felhőbeállítások lehetőséget.**
2. Tiltsa le a **felhő feltöltési és letöltési**.
3. Ha az eszközt részlegesen leválasztott módban szeretné futtatni, engedélyezze az **Azure Portal-kezelés szolgáltatást.**

    ![Csatlakoztatási mód](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Ha az eszközt leválasztott módban szeretné futtatni, tiltsa le az **Azure Portal-kezelést.** Most az eszköz csak a helyi webes felhasználói felületen keresztül kezelhető.

    ![Csatlakoztatási mód](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Energiakezelése

A virtuális eszközt leállíthatja vagy újraindíthatja a helyi webes felhasználói felület használatával. Az újraindítás előtt ajánlott offline állapotba állítani a gazdagépen található megosztásokat, majd magát az eszközt is. Ez a művelet minimálisra csökkenti az adatsérülés lehetőségét.

1. A helyi webes felhasználói felületen nyissa meg **a Karbantartás > energiagazdálkodási beállítások lehetőséget.**
2. Kattintson a **Leállítás** vagy **az Újraindítás** gombra attól függően, hogy mit kíván tenni.

    ![Energiaellátási beállítások](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Amikor megerősítést kér, a folytatáshoz kattintson az **Igen** gombra.

> [!NOTE]
> Ha leállítja a virtuális eszközt, el kell indítania az eszközt a hipervizor kezelésén keresztül.
