---
title: Az Azure Migrate berendezés telepítésének és felderítésének – problémamegoldása
description: Segítség az Azure Migrate berendezés üzembe helyezéséhez és a gépek felderítéséhez.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 6cb83a87f2e96eb62696e5d92095ef2b8d7c7def
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677326"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Az Azure Migrate-berendezés és felderítés – problémamegoldás

Ez a cikk segít az [Azure Migrate](migrate-services-overview.md) berendezés üzembe helyezésekor és a készülék helyszíni gépek felderítése során felmerülő problémák elhárításához.


## <a name="whats-supported"></a>Mik támogatottak?

[Tekintse át](migrate-appliance.md) a készülék támogatási követelményeit.


## <a name="invalid-ovf-manifest-entry"></a>"Érvénytelen OVF jegyzékjegyzékbejegyzés"

Ha a következő hibaüzenet jelenik meg: "A megadott jegyzékfájl érvénytelen: Érvénytelen OVF jegyzékfájl", tegye a következőket:

1. Ellenőrizze, hogy az Azure Migrate appliance OVA fájl megfelelően van-e letöltve a kivonatérték ének ellenőrzésével. [További információ](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Ha a kivonatoló érték nem egyezik, töltse le újra a OVA-fájlt, és próbálkozzon újra a központi telepítéssel.
2. Ha az üzembe helyezés továbbra is sikertelen, és a VMware vSphere-ügyféllel telepíti az OVF-fájlt, próbálja meg telepíteni a vSphere webes ügyfélen keresztül. Ha a telepítés továbbra is sikertelen, próbálkozzon egy másik webböngészővel.
3. Ha a vSphere webügyfelet használja, és a vCenter Server 6.5-ös vagy 6.7-es verzióján próbálja meg telepíteni, próbálja meg a OVA-t közvetlenül az ESXi-állomásra telepíteni:
   - Csatlakozzon az ESXi-állomáshoz közvetlenül (a vCenter Server helyett) a webes ügyféllel (https://<>/ui *gazdaip-cím).*
   - A **Home** > **Home Inventory**alkalmazásban válassza a **Fájltelepítése** > **OVF sablont**. Tallózással keresse meg az OVA-t, és fejezze be a telepítést.
4. Ha a központi telepítés továbbra is sikertelen, forduljon az Azure Áttelepítési támogatási szolgálathoz.

## <a name="cant-connect-to-the-internet"></a>Nem lehet csatlakozni az internethez

Ez akkor fordulhat elő, ha a készülék proxy mögött van.

- Győződjön meg arról, hogy megadja az engedélyezési hitelesítő adatokat, ha a proxynak szüksége van rájuk.
- Ha url-alapú tűzfalproxyt használ a kimenő kapcsolatok szabályozására, adja hozzá [ezeket az URL-címeket](migrate-appliance.md#url-access) egy engedélyezési listához.
- Ha az internethez való csatlakozáshoz elfogó proxyt használ, importálja a proxytanúsítványt a készülék virtuális gépére [az alábbi lépésekkel.](https://docs.microsoft.com/azure/migrate/concepts-collector)

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Nem lehet bejelentkezni az Azure-ba a készülék webalkalmazásból

A "Sajnáljuk, de nem sikerül bejelentkezni" hibaüzenet jelenik meg, ha helytelen Azure-fiókot használ az Azure-ba való bejelentkezéshez. Ennek a hibának több oka is van:

- Ha bejelentkezik a készülék webalkalmazás a nyilvános felhőben, a felhasználói fiók hitelesítő adatait a kormány felhőportálon.
- Ha a magánfelhő-portál felhasználói fiókhitelesítő adataival jelentkezik be a kormányzati felhőhöz készült eszköz webalkalmazásába.

Győződjön meg arról, hogy a megfelelő hitelesítő adatokat használja.

##  <a name="datetime-synchronization-error"></a>Dátum-idő szinkronizálási hiba

A dátum- és időszinkronizálással kapcsolatos hiba (802) azt jelzi, hogy a kiszolgáló órája öt percnél tovább nem volt szinkronban az aktuális idővel. Módosítsa az óraidőt a gyűjtő virtuális gépén az aktuális időnek megfelelően:

1. Nyisson meg egy rendszergazdai parancssort a virtuális gépen.
2. Az időzóna ellenőrzéséhez futtassa a **w32tm /tz kapcsolót.**
3. Az idő szinkronizálásához futtassa a **w32tm /resync kapcsolót.**


## <a name="unabletoconnecttoserver"></a>"Nem lehetcsatlakozni"

Ha ez a csatlakozási hiba jelenik meg, előfordulhat, hogy nem tud csatlakozni a vCenter Server Servername .com:9443.com.if you get this connection error, you might be unable to connect to vCenter *Server Servername*.com:9443. A hiba részletei azt jelzik, hogy `https://\*servername*.com:9443/sdk` nincs végpont figyel, amely képes elfogadni az üzenetet.

- Ellenőrizze, hogy a készülék legújabb verzióját futtatja-e. Ha nem, frissítse a készüléket a [legújabb verzióra](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Ha a probléma továbbra is a legújabb verzióban jelentkezik, előfordulhat, hogy a készülék nem tudja feloldani a megadott vCenter-kiszolgáló nevet, vagy a megadott port hibás. Alapértelmezés szerint, ha a port nincs megadva, a gyűjtő megpróbál csatlakozni a 443-as porthoz.

    1. Ping *Servername*.com a készülékről.
    2. Ha az 1.
    3. Azonosítsa a vCenter-kiszolgálóhoz csatlakozni hozandó megfelelő portszámot.
    4. Ellenőrizze, hogy a vCenter Server működik-e.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>60052/60039-es hiba: Előfordulhat, hogy a készülék nincs regisztrálva

- 60052-es hiba: "Előfordulhat, hogy a készülék nincs sikeresen regisztrálva az Azure Migrate projekthez" akkor fordul elő, ha a készülék regisztrálásához használt Azure-fiók nem rendelkezik megfelelő engedélyekkel.
    - Győződjön meg arról, hogy a készülék regisztrálásához használt Azure-felhasználói fiók rendelkezik legalább közreműködői engedélyekkel az előfizetéshez.
    - [További információ](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) a szükséges Azure-szerepkörökről és engedélyekről.
- 60039-es hiba: "Előfordulhat, hogy a készülék nincs sikeresen regisztrálva az Azure Migrate projekthez" – előfordulhat, ha a regisztráció sikertelen, mert a készülék regisztrálásához használt Azure Migrate projekt nem található.
    - Az Azure Portalon, és ellenőrizze, hogy a projekt létezik-e az erőforráscsoportban.
    - Ha a projekt nem létezik, hozzon létre egy új Azure Migrate projektet az erőforráscsoportban, és regisztrálja újra a készüléket. [További információ új](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) projekt létrehozásáról.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>60030/60031-es hiba: A key vault felügyeleti művelete nem sikerült

Ha a 60030-as vagy 60031-es hibaüzenet "Egy Sikertelen Azure Key Vault-felügyeleti művelet" hibaüzenet jelenik meg, tegye a következőket:
- Győződjön meg arról, hogy a készülék regisztrálásához használt Azure-felhasználói fiók rendelkezik legalább közreműködői engedélyekkel az előfizetéshez.
- Győződjön meg arról, hogy a fiók rendelkezik a hibaüzenetben megadott key vault hozzáféréssel, majd próbálkozzon újra a művelettel.
- Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához.
- [További információ](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) a szükséges Azure-szerepkörökről és engedélyekről.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>60028-as hiba: A felderítés nem indítható el

60028-as hiba: "A felderítés hiba miatt nem indítható. A művelet nem sikerült a megadott gazdagépek vagy fürtök listájához" azt jelzi, hogy a felderítés nem indítható el a hibában felsorolt állomásokon, mert probléma van a virtuális gép adatainak elérésében vagy beolvasásában. A többi állomás hozzáadása sikeresen megtörtént.

- Adja hozzá újra a hibában felsorolt állomásokat az **Állomás hozzáadása** beállítással.
- Ha érvényesítési hiba történt, tekintse át a javítási útmutatót a hibák kijavításához, majd próbálkozzon újra a **Felderítés mentése és indítása** lehetőséggel.

## <a name="error-60025-azure-ad-operation-failed"></a>60025-ös hiba: Az Azure AD-művelet nem sikerült 
60025-ös hiba: "Egy Azure AD-művelet nem sikerült. A hiba az Azure AD-alkalmazás létrehozása vagy frissítése közben történt" akkor fordul elő, amikor a felderítés kezdeményezéséhez használt Azure felhasználói fiók eltér a készülék regisztrálásához használt fiók. Tegye a következők egyikét:

- Győződjön meg arról, hogy a felderítést megcélzó felhasználói fiók megegyezik a készülék regisztrálásához használt fiókkal.
- Adja meg az Azure Active Directory alkalmazáshozzáférési engedélyeket ahhoz a felhasználói fiókhoz, amelynek felderítési művelete sikertelen.
- Törölje az Azure Migrate projekthez korábban létrehozott erőforráscsoportot. Hozzon létre egy másik erőforráscsoportot az újrakezdéshez.
- [További információ](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) az Azure Active Directory alkalmazásengedélyeiről.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>50004-es hiba: Nem lehet csatlakozni az állomáshoz vagy a fürthöz

50004-es hiba: "Nem lehet csatlakozni egy állomáshoz vagy fürthöz, mert a kiszolgálónév nem oldható fel. WinRM-hibakód: 0x803381B9" " akkor fordulhat elő, ha a készülék Hez tartozó Azure DNS-szolgáltatás nem tudja feloldani a megadott fürt- vagy állomásnevet.

- Ha ez a hiba jelenik meg a fürtön, fürt teljes tartományn.
- Ez a hiba a fürtben lévő állomásokesetében is megjelenhet. Ez azt jelzi, hogy a készülék csatlakozhat a fürthöz, de a fürt nem teljes tartományneveket ad vissza. A hiba megoldásához frissítse a készülék gazdagépfájlját az IP-cím és az állomásnevek leképezésével:
    1. Nyissa meg a Jegyzettömbet rendszergazdaként.
    2. Nyissa meg a C:\Windows\System32\Drivers\etc\hosts fájlt.
    3. Adja hozzá az IP-címet és az állomásnevet egy sorban. Ismételje meg a műveletet minden olyan állomásnál vagy fürtnél, ahol ez a hiba megjelenik.
    4. Mentse és zárja be a hosts fájlt.
    5. Ellenőrizze, hogy a készülék tud-e csatlakozni a gazdagépekhez a készülékkezelő alkalmazás segítségével. 30 perc elteltével meg kell jelennie az Azure Portalon az üzemeltetés legfrissebb információinak.

## <a name="discovered-vms-not-in-portal"></a>A portálon nem található felderített virtuális gépek

Ha a felderítési állapot "Felderítés folyamatban van", de még nem látja a virtuális gépeket a portálon, várjon néhány percet:
- A VMware VM körülbelül 15 percet vesz igénybe.
- A Hyper-V VM-felderítés hez hozzáadott gazdagépek körülbelül két percet vesz igénybe.

Ha vár, és az állapot nem változik, válassza a **Frissítés** lehetőséget a **Kiszolgálók** lapon. Ez az Azure Migrate: Server Assessment és az Azure Migrate: Server Migration: Server Migration felderített kiszolgálóinak számát mutatja.

Ha ez nem működik, és a VMware-kiszolgálókat fedezi fel:

- Ellenőrizze, hogy a megadott vCenter-fiók megfelelően van-e beállítva, és legalább egy virtuális géphez hozzáfér.
- Az Azure Migrate nem tudja felderíteni a VMware virtuális gépeket, ha a vCenter-fiók rendelkezik a vCenter vM mappa szintjén biztosított hozzáféréssel. [További információ](set-discovery-scope.md) a hatókörfelderítésről.

## <a name="vm-data-not-in-portal"></a>Virtuálisgép-adatok nem a portálon

Ha a felderített virtuális gépek nem jelennek meg a portálon, vagy ha a virtuális gép adatai elavultak, várjon néhány percet. A felderített virtuálisgép-konfigurációs adatok változásai a portálon akár 30 percet is igénybe vesznek. Az alkalmazásadatok változásai nak megjelenése néhány órát is igénybe vehet. Ha ez után az adatok nem jelennek meg, próbálkozzon a frissítési lehetőségekkel:

1. A **Kiszolgálók** > **Azure Áttelepítési kiszolgáló értékelése területén**válassza az **Áttekintés**lehetőséget.
2. A **Kezelés csoportban**válassza **az Ügynök állapota**lehetőséget.
3. Válassza **az Ügynök frissítése**lehetőséget.
4. Várja meg, amíg befejeződik a frissítési művelet. Most már naprakész információkat kell látnia.

## <a name="deleted-vms-appear-in-portal"></a>Törölt virtuális gépek jelennek meg a portálon

Ha törli a virtuális gépeket, és azok továbbra is megjelennek a portálon, várjon 30 percet. Ha továbbra is megjelennek, frissítse a fent leírt módon.

## <a name="common-app-discovery-errors"></a>Gyakori alkalmazásfelderítési hibák

Az Azure Migrate támogatja az alkalmazások, szerepkörök és funkciók felderítését az Azure Migrate: Server Assessment használatával. Az alkalmazásfelderítés jelenleg csak a VMware számára támogatott. [További információ](how-to-discover-applications.md) az alkalmazásfelderítés beállításának követelményeiről és lépéseiről.

A tipikus alkalmazásfelderítési hibák a táblázatban vannak összesítve. 

**Hiba** | **Okoz** | **Művelet**
--- | --- | --- | ---
10000: "Nem lehet felderíteni a kiszolgálón telepített alkalmazásokat". | Ez akkor fordulhat elő, ha a gép operációs rendszere nem Windows vagy Linux. | Csak Windows/Linux alkalmazásfelderítést használjon.
10001: "Nem lehet letölteni a telepített alkalmazások a szerver". | Belső hiba - néhány hiányzó fájl a készülékben. | Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával.
10002: "Nem lehet letölteni a telepített alkalmazások a szerver". | Lehet, hogy a készülék felderítési ügynöke nem működik megfelelően. | Ha a probléma nem oldódik meg 24 órán belül, forduljon az ügyfélszolgálathoz.
10003 "Nem lehet letölteni a telepített alkalmazások a szerver". | Lehet, hogy a készülék felderítési ügynöke nem működik megfelelően. | Ha a probléma nem oldódik meg 24 órán belül, forduljon az ügyfélszolgálathoz.
10004: "Nem lehet felfedezni a telepített alkalmazásokat <Windows/Linux> gépekszámára." |  A Windows/Linux> gépek <hez való hozzáféréshez szükséges hitelesítő adatok nem voltak megadva a készülékben.| Adjon hozzá hitelesítő adatokat a készülékhez, amely hozzáfér a Windows/Linux> <.
10005: "Nem lehet elérni a helyszíni kiszolgálót". | Lehet, hogy a hozzáférési hitelesítő adatok hibásak. | Frissítse a készülék hitelesítő adatait győződjön meg róla, hogy hozzáférhet a megfelelő géphez velük. 
10006: "Nem lehet elérni a helyszíni kiszolgálót". | Ez akkor fordulhat elő, ha a gép operációs rendszere nem Windows vagy Linux.|  Csak Windows/Linux alkalmazásfelderítést használjon.
10007: "Nem lehet feldolgozni a beolvasott metaadatokat" | Ez a belső hiba a JSON deszerializálásának megkísérlése közben történt | Megoldásesetén forduljon a Microsoft támogatási szolgálatához
9000/9001/9002: "Nem lehet felderíteni a kiszolgálón telepített alkalmazásokat". | Előfordulhat, hogy a VMware-eszközök nincsenek telepítve, vagy sérültek. | Telepítse/telepítse újra a VMware eszközöket a megfelelő gépen, és ellenőrizze, hogy fut-e.
9003: Nem lehet felderíteni a kiszolgálón telepített alkalmazásokat". | Ez akkor fordulhat elő, ha a gép operációs rendszere nem Windows vagy Linux. | Csak Windows/Linux alkalmazásfelderítést használjon.
9004: "Nem lehet felderíteni a kiszolgálón telepített alkalmazásokat". | Ez akkor fordulhat elő, ha a virtuális gép ki van kapcsolva. | Felderítéséhez győződjön meg arról, hogy a virtuális gép be van kapcsolva.
9005: "Nem lehet felderíteni a virtuális gépre telepített alkalmazásokat. | Ez akkor fordulhat elő, ha a gép operációs rendszere nem Windows vagy Linux. | Csak Windows/Linux alkalmazásfelderítést használjon.
9006/9007: "Nem lehet letölteni a telepített alkalmazások a szerver". | Lehet, hogy a készülék felderítési ügynöke nem működik megfelelően. | Ha a probléma nem oldódik meg 24 órán belül, forduljon az ügyfélszolgálathoz.
9008: "Nem lehet letölteni a telepített alkalmazások a szerver". | Lehet, hogy belső hiba.  | Tf a probléma nem oldja meg magát 24 órán belül, forduljon az ügyfélszolgálathoz.
9009: "Nem lehet letölteni a telepített alkalmazások a szerver". | Akkor fordulhat elő, ha a Windows felhasználói fiókok felügyelete (UAC) beállításai a kiszolgálón korlátozóak, és megakadályozzák a telepített alkalmazások felderítését. | Keressen rá a "Felhasználói fiókok felügyelete" beállításokra a kiszolgálón, és állítsa be a felhasználói fiókok felügyelete beállítást a kiszolgálón az alsó két szint egyikére.
9010: "Nem lehet letölteni a telepített alkalmazások a szerver". | Lehet, hogy belső hiba.  | Tf a probléma nem oldja meg magát 24 órán belül, forduljon az ügyfélszolgálathoz.
9011: "A vendégtől letölthető fájl nem található meg a vendég virtuális gépen" | A problémát belső hiba okozhat. | A probléma 24 órán belül automatikusan megoldódik. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához.
9012: "Az eredményfájl tartalma üres." | A problémát belső hiba okozhat. | A probléma 24 órán belül automatikusan megoldódik. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához.
9013: "Új ideiglenes profil jön létre a VMware VM minden bejelentkezéséhez" | Új ideiglenes profil jön létre a virtuális gépbe való minden bejelentkezéshez | Győződjön meg arról, hogy a vendég virtuális gép hitelesítő adataiban megadott felhasználónév UPN formátumú.
9015: "Nem lehet csatlakozni a VMware virtuális gépekhez a vCenter elégtelen jogosultságai miatt" | A vendégműveletek szerepkör nincs engedélyezve a vCenter felhasználói fiókjában | Győződjön meg arról, hogy a Vendég műveletek szerepkör engedélyezve van a vCenter felhasználói fiókjában.
9016: "Nem lehet csatlakozni a VMware virtuális gépekhez, mert a vendégműveleti ügynök nek nincs adata" | A VMware-eszközök nincsenek megfelelően telepítve, vagy nincsenek naprakészek. | Győződjön meg arról, hogy a VMware-eszközök megfelelően vannak telepítve és naprakészek.
9017: "A felderített metaadatokat kiszolgáló fájl nem található a virtuális gépen." | A problémát belső hiba okozhat. | Megoldásért forduljon a Microsoft támogatási szolgálatához.
9018: "A PowerShell nincs telepítve a vendég virtuális gépeken." | A PowerShell nem érhető el a vendég virtuális gép. | Telepítse a PowerShellt a vendég virtuális gépben.
9019: "Nem lehet felfedezni a vendég virtuális gép működési hibái miatt" | A VMware vendégművelete nem sikerült a virtuális számítógépen. | Győződjön meg arról, hogy a virtuális gép hitelesítő adatai érvényesek, és a vendég virtuális gép hitelesítő adataiban megadott felhasználónév UPN formátumú.
9020: "A fájllétrehozási engedély megtagadva." | A felhasználóhoz vagy a csoportházirendhez társított szerepkör korlátozza a felhasználót a fájl mappában való létrehozására | Ellenőrizze, hogy a megadott vendégfelhasználó rendelkezik-e létrehozási engedéllyel a mappában lévő fájlhoz. A mappa nevét lásd: **Értesítések** a kiszolgálóértékelésben.
9021: "A fájllétrehozési engedély megtagadva a System Temp Path mappában." | A VMware eszközverziója a virtuális gépen nem támogatott | Frissítse a VMware eszköz verzióját 10.2.0 fölé.
9022: "A WMI-objektumhoz való hozzáférés megtagadása." | A felhasználóhoz vagy a csoportházirendhez társított szerepkör korlátozza a felhasználót a WMI-objektum elérésére. | Forduljon a Microsoft támogatási szolgálatához.
9023: "A SystemRoot környezeti változó értéke üres." | Nem ismert | Forduljon a Microsoft támogatási szolgálatához.
9024: "A TEMP környezeti változó értéke üres." | Nem ismert | Forduljon a Microsoft támogatási szolgálatához.
9025: "A PowerShell sérült a vendég virtuális gépekben." | Nem ismert | Telepítse újra a PowerShellt a vendég virtuális gépben, és ellenőrizze, hogy a PowerShell futtatható-e a vendég virtuális számítógépen.
8084: "Nem lehet felfedezni alkalmazások miatt <Exception from VMware>VMware hiba: " | Az Azure Migrate készülék VMware API-kat használ az alkalmazások felderítéséhez. Ez a probléma akkor fordulhat elő, ha a vCenter Server kivételt okoz az alkalmazások felderítése közben. A VMware hibaüzenete megjelenik a portálon megjelenő hibaüzenetben. | Keresse meg az üzenetet a [VMware dokumentációjában,](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)és kövesse a javításhoz szükséges lépéseket. Ha nem tudja a javítást, forduljon a Microsoft támogatási szolgálatához.



## <a name="next-steps"></a>További lépések
Állítsa be a készüléket [VMware,](how-to-set-up-appliance-vmware.md) [Hyper-V](how-to-set-up-appliance-hyper-v.md)vagy [fizikai kiszolgálókhoz](how-to-set-up-appliance-physical.md).
