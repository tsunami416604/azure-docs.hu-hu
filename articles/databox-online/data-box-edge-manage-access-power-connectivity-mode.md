---
title: Azure Data Box Edge eszközhozzáférési, táp- és csatlakoztatási mód | Microsoft dokumentumok
description: Bemutatja, hogyan kezelhetők a hozzáférés, a tápellátás és a kapcsolódási mód az Azure Data Box Edge eszközhöz, amely segít az adatok azure-ba való átvitelének
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: be4b780161003470622cb367d78138cfeffe341b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454332"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>Hozzáférés, tápellátás és kapcsolati mód kezelése az Azure Data Box Edge számára

Ez a cikk ismerteti, hogyan kezelheti a hozzáférési, tápellátás átkapcsolási és kapcsolódási módot az Azure Data Box Edge. Ezek a műveletek a helyi webes felhasználói felületen vagy az Azure Portalon keresztül hajtják végre.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Eszközhozzáférés kezelése
> * Kapcsolódási mód kezelése
> * Energiakezelése


## <a name="manage-device-access"></a>Eszközhozzáférés kezelése

A Data Box Edge eszközhöz való hozzáférést egy eszközjelszó szabályozza. A jelszót a helyi internetes felhasználói felületen módosíthatja. Az eszköz jelszavát is alaphelyzetbe állíthatja az Azure Portalon.

### <a name="change-device-password"></a>Az eszköz jelszavának módosítása

Az eszköz jelszavának módosításához kövesse a helyi felhasználói felület alábbi lépéseit.

1. A helyi webes felhasználói felületen nyissa meg **a Karbantartás > jelszómódosítás című**részt.
2. Írja be az aktuális jelszót, majd az új jelszót. A megadott jelszónak 8 és 16 karakter között kell lennie. A jelszónak a következő karakterek közül 3-at kell tartalmaznia: nagybetűs, kisbetűs, numerikus és speciális karakterek. Erősítse meg az új jelszót.

    ![Change password](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Válassza **a Jelszó módosítása**lehetőséget.
 
### <a name="reset-device-password"></a>Eszközjelszavának alaphelyzetbe állítása

A visszaállítási munkafolyamat nem követeli meg a felhasználótól a régi jelszó visszahívását, és akkor hasznos, ha a jelszó elvész. Ez a munkafolyamat az Azure Portalon történik.

1. Az Azure Portalon nyissa **meg az Áttekintés > rendszergazdai jelszó alaphelyzetbe állítása című témakört.**

    ![Új jelszó létrehozása](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Írja be az új jelszót, majd erősítse meg. A megadott jelszónak 8 és 16 karakter között kell lennie. A jelszónak a következő karakterek közül 3-at kell tartalmaznia: nagybetűs, kisbetűs, numerikus és speciális karakterek. Válassza az **Alaphelyzetbe állítás** lehetőséget.

    ![Új jelszó létrehozása](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Erőforrás-hozzáférés kezelése

A Data Box Edge/Data Box Gateway, az IoT Hub és az Azure Storage-erőforrás létrehozásához közreműködőként vagy magasabb szintű erőforráscsoport-szintű engedélyekre van szüksége. A megfelelő erőforrás-szolgáltatók regisztrálása is szükséges. Minden olyan művelethez, amely aktiválási kulcsot és hitelesítő adatokat tartalmaz, a Microsoft Graph API-hoz is engedélyeket kell kérni. Ezeket a következő szakaszok ismertetik.

### <a name="manage-microsoft-graph-api-permissions"></a>A Microsoft Graph API-engedélyeinek kezelése

A Data Box Edge-eszköz aktiválási kulcsának létrehozásakor vagy a hitelesítő adatokat igénylő műveletek végrehajtásakor a Microsoft Graph API-hoz engedélyekre van szüksége. A hitelesítő adatokat igénylő műveletek a következők lehetnek:

-  Megosztás létrehozása társított tárfiókkal.
-  Olyan felhasználó létrehozása, aki hozzáférhet az eszközön lévő megosztásokhoz.

Hozzáféréssel kell `User` rendelkeznie az Active Directory-bérlőhöz, ahogy ahhoz `Read all directory objects`szükséges. Nem lehet vendégfelhasználó, mert nincs engedélye a `Read all directory objects`alkalmazáshoz. Ha vendég vagy, akkor az olyan műveletek, mint az aktiválási kulcs létrehozása, a Data Box Edge-eszközön való megosztás létrehozása, egy felhasználó létrehozása, az Edge számítási szerepkörkonfigurációja, az eszköz jelszavának visszaállítása mind sikertelen ek lesznek.

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

Az alapértelmezett teljesen csatlakoztatott módon kívül az eszköz részlegesen csatlakoztatott vagy teljesen leválasztott módban is futtatható. E módok mindegyikét az alábbiak szerint ismertetjük:

- **Teljesen csatlakoztatva** - Ez a normál alapértelmezett mód, amelyben az eszköz működik. Ebben a módban mind a felhőalapú feltöltés, mind az adatok letöltése engedélyezve van. Az Azure Portalon vagy a helyi webes felhasználói felületen kezelheti az eszközt.

- **Részlegesen leválasztva** – Ebben a módban az eszköz nem tud feltölteni vagy letölteni a megosztási adatokat, azonban az Azure Portalon keresztül kezelhető.

    Ez a mód általában akkor használatos, ha forgalmi díjas műholdas hálózaton van, és a cél a hálózati sávszélesség-felhasználás minimalizálása. Az eszközfigyelési műveletek nél továbbra is előfordulhat minimális hálózati felhasználás.

- **Leválasztva** – Ebben a módban az eszköz teljesen le van választva a felhőről, és mind a felhőbeli feltöltések, mind a letöltések le vannak tiltva. Az eszköz csak a helyi webes felhasználói felületen keresztül kezelhető.

    Ezt a módot általában akkor használja, ha az eszközt offline állapotba szeretné helyezni.

Az eszközmód módosításához hajtsa végre az alábbi lépéseket:

1. Az eszköz helyi webes felhasználói felületén válassza a **Konfigurációs > felhőbeállítások lehetőséget.**
2. A legördülő listában válassza ki azt a módot, amelyben az eszközt működtetni szeretné. Választhat a **Teljesen csatlakoztatva**, **Részben csatlakoztatva**és **teljesen leválasztott**. Ha az eszközt részlegesen leválasztott módban szeretné futtatni, engedélyezze az **Azure Portal-kezelés szolgáltatást.**

    ![Csatlakoztatási mód](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Energiakezelése

A helyi webes felhasználói felület használatával leállíthatja vagy újraindíthatja a fizikai eszközt. Azt javasoljuk, hogy az újraindítás előtt tegye offline állapotba a megosztásokat az adatkiszolgálón, majd az eszközön. Ez a művelet minimálisra csökkenti az adatsérülés lehetőségét.

1. A helyi webes felhasználói felületen nyissa meg **a Karbantartás > energiagazdálkodási beállítások lehetőséget.**
2. Válassza **a Leállítás** vagy **az Újraindítás** lehetőséget attól függően, hogy mit kíván tenni.

    ![Energiaellátási beállítások](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Amikor megerősítést kér, a folytatáshoz válassza az **Igen** lehetőséget.

> [!NOTE]
> Ha leállítja a fizikai eszközt, a bekapcsoláshoz meg kell nyomnia az eszköz bekapcsológombját.

## <a name="next-steps"></a>További lépések

- További információ a [megosztások kezeléséről](data-box-edge-manage-shares.md).
