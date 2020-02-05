---
title: Azure VMware Solutions (AVS) – AVS Private Cloud engedélyezési modell
description: Az AVS Private Cloud engedély-modell,-csoportok és-kategóriák leírása
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9488c59ead23fb68633ccc56a0df905ebfeea079
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014946"
---
# <a name="avs-private-cloud-permission-model-of-vmware-vcenter"></a>Az AVS Private Cloud engedélyezési modellje VMware vCenter

Az AVS teljes körű rendszergazdai hozzáférést tart fenn az AVS Private Cloud-környezethez. Minden AVS-ügyfél megfelelő rendszergazdai jogosultságokkal rendelkezik ahhoz, hogy a virtuális gépeket a környezetében lehessen üzembe helyezni és felügyelni. Ha szükséges, átmenetileg kiterjesztheti a jogosultságokat a rendszergazdai funkciók végrehajtásához.

## <a name="cloud-owner"></a>Felhő tulajdonosa

Ha létrehoz egy AVS Private-felhőt, a rendszer létrehoz egy **CloudOwner** -felhasználót a vCenter egyszeri bejelentkezési tartományában, amely a **felhőalapú tulajdonosi szerepkör** -hozzáférés használatával kezeli az objektumokat az AVS Private Cloud-ban. Ez a felhasználó további [vCenter-identitási forrásokat](set-vcenter-identity.md)és más felhasználókat is BEÁLLÍTHAT az AVS Private Cloud vCenter.

> [!NOTE]
> Az AVS Private Cloud vCenter alapértelmezett felhasználója cloudowner@AVS.local, ha egy AVS Private Cloud-t hoz létre.

## <a name="user-groups"></a>Felhasználói csoportok

Egy **Cloud-Owner-Group** nevű csoport jön létre egy AVS Private Cloud üzembe helyezése során. Az ebben a csoportban lévő felhasználók felügyelhetik a vSphere-környezet különböző részeit az AVS Private Cloud szolgáltatásban. Ez a csoport automatikusan megkapja a **felhőalapú tulajdonosi szerepkörhöz** tartozó jogosultságokat, és a **CloudOwner** -felhasználót a csoport tagjaként adja hozzá a rendszer. Az AVS olyan további csoportokat hoz létre, amelyek korlátozott jogosultságokkal rendelkeznek a könnyű kezelés érdekében. Hozzáadhat felhasználókat az előre létrehozott csoportokhoz, és az alább megadott jogosultságokat a rendszer automatikusan hozzárendeli a csoportokhoz tartozó felhasználókhoz.

### <a name="pre-created-groups"></a>Előre létrehozott csoportok

| Csoport neve | Rendeltetés | Szerepkör |
| -------- | ------- | ------ |
| Felhőbeli tulajdonos – csoport | Ennek a csoportnak a tagjai rendszergazdai jogosultságokkal rendelkeznek az AVS Private Cloud vCenter | [Felhőbeli tulajdonos – szerepkör](#cloud-owner-role) |
| Felhő-globális-fürt-rendszergazda-csoport | Ennek a csoportnak a tagjai rendszergazdai jogosultságokkal rendelkeznek az AVS Private Cloud vCenter-fürtön | [Felhő – fürt – rendszergazda – szerepkör](#cloud-cluster-admin-role) |
| Cloud-Global-Storage-felügyeleti csoport | A csoport tagjai kezelhetik a tárolót az AVS Private Cloud vCenter | [Felhőalapú tárolás – rendszergazda – szerepkör](#cloud-storage-admin-role) |
| Cloud-Global-Network-admin-Group | Ennek a csoportnak a tagjai kezelhetik a hálózati és az elosztott portok csoportjait az AVS Private Cloud vCenter | [Cloud-Network-admin-role](#cloud-network-admin-role) |
| Cloud-Global-VM-admin-Group | A csoport tagjai kezelhetik a virtuális gépeket az AVS Private Cloud vCenter | [Cloud-VM-admin-role](#cloud-vm-admin-role) |

Ha az egyéni felhasználók számára engedélyezni szeretné az AVS privát felhő felügyeletét, hozzon létre felhasználói fiókokat a megfelelő csoportok hozzáadásához.

> [!CAUTION]
> Az új felhasználókat csak a *Cloud-Owner-Group*, a *Cloud-Global-cluster-admin-Group*, a *Cloud-Global-Storage-admin-Group*, a *Cloud-Global-Network-admin-Group* vagy a *Cloud-Global-VM-admin-Group*szolgáltatáshoz kell hozzáadni.  A *rendszergazdák* csoportba felvett felhasználók automatikusan el lesznek távolítva.  A vSphere webes felhasználói felületén csak a szolgáltatási fiókokat kell felvenni a *rendszergazdák* csoportjába, és a szolgáltatásfiókok nem használhatók.

## <a name="list-of-vcenter-privileges-for-default-roles"></a>Az alapértelmezett szerepkörökhöz tartozó vCenter-jogosultságok listája

### <a name="cloud-owner-role"></a>Felhőbeli tulajdonos – szerepkör

| **Kategória** | **Jogosultság** |
|----------|-----------|
| **Riasztások** | Riasztás nyugtázása <br> Riasztás létrehozása <br> Riasztási művelet letiltása <br> Riasztás módosítása <br> Riasztás eltávolítása <br> Riasztás állapotának beállítása |
| **Engedélyek** | Módosítási engedély |
| **Tartalom könyvtára** | Függvénytár-elem hozzáadása <br> Helyi könyvtár létrehozása <br> Előfizetett tár létrehozása <br> Függvénytár-elemek törlése <br> Helyi könyvtár törlése <br> Előfizetett tár törlése <br> Fájlok letöltése <br> Függvénytár-elemek kizárása <br> Előfizetett tár kizárása <br> Tároló importálása <br> Mintavételi előfizetés adatai <br> Tár olvasása <br> Könyvtár-elemek szinkronizálása <br> Előfizetett tár szinkronizálása <br> Betekintés típusa <br> Konfigurációs beállítások frissítése <br> Fájlok frissítése <br> Könyvtár frissítése <br> Függvénytár-elemek frissítése <br> Helyi könyvtár frissítése <br> Előfizetett tár frissítése <br> Konfigurációs beállítások megtekintése |
| **Titkosítási műveletek** | Lemez hozzáadása <br> Klónozás <br> Visszafejtés <br> Közvetlen hozzáférés <br> Titkosítás <br> Új titkosítás <br> KMS kezelése <br> Titkosítási házirendek kezelése <br> Kulcsok kezelése <br> Migrate (Áttelepítés) <br> Recrypt <br> Virtuális gép regisztrálása <br> Gazdagép regisztrálása |
| **dvPort csoport** | Létrehozás <br> Törlés <br> Módosítás <br> Házirend-művelet <br> Hatókör-művelet |
| **Datastore** | Tárterület foglalása <br> Adattár tallózása <br> Adattár konfigurálása <br> Alacsony szintű fájlok műveletei <br> Adattár áthelyezése <br> Adattár eltávolítása <br> Fájl eltávolítása <br> Adattár átnevezése <br> Virtuálisgép-fájlok frissítése <br> Virtuális gép metaadatainak frissítése |
| **ESX-ügynök kezelője** | Config <br> Módosítás <br> Megtekintés |
| **Kiterjesztés** | Bővítmény regisztrálása <br> Bővítmény regisztrációjának törlése <br> Bővítmény frissítése |
| **Külső statisztikai szolgáltató**| Regisztráció <br> Regisztrálás törlése <br> Frissítés |
| **Mappa** | Mappa létrehozása <br> Mappa törlése <br> Mappa áthelyezése <br> Mappa átnevezése |
| **Globális** | Feladat megszakítása <br> Kapacitástervezés <br> Diagnosztika <br> Metódusok letiltása <br> Metódusok engedélyezése <br> Globális címke <br> Egészségügy <br> Licencek <br> Esemény naplózása <br> Egyéni attribútumok kezelése <br> Proxy <br> Parancsfájl művelete <br> Service managerek <br> Egyéni attribútum beállítása <br> Rendszercímke |
| **Állapot-frissítési szolgáltató** | Regisztráció <br> Regisztrálás törlése <br> Frissítés |
| **Gazdagép > konfigurálása** | Tárolási partíció konfigurációja |
| **Gazdagép > leltározása** | Fürt módosítása |
| **vSphere címkézése** | VSphere-címke hozzárendelésének vagy hozzárendelésének megszüntetése <br> VSphere címke létrehozása <br> VSphere-címke kategória létrehozása <br> VSphere-címke törlése <br> VSphere törlése <br> VSphere címke szerkesztése <br> VSphere szerkesztése címke kategóriája <br> Kategória UsedBy-mezőjének módosítása <br> Címke UsedBy-mezőjének módosítása |
| **Hálózat** | Hálózat kiosztása <br> Konfigurálás <br> Hálózat áthelyezése <br> Eltávolítás |
| **Teljesítmény** | Intervallumok módosítása |
| **Gazdagép profilja** | Megtekintés |
| **Erőforrás** | Javaslat alkalmazása <br> VApp erőforrás-készlethez rendelése <br> Virtuális gép erőforrás-készlethez rendelése <br> Erőforráskészlet létrehozása <br> Virtuális gép migrálása <br> Migrálás virtuális gépen <br> Erőforráskészlet módosítása <br> Erőforráskészlet áthelyezése <br> VMotion lekérdezése <br> Erőforráskészlet eltávolítása <br> Erőforráskészlet átnevezése |
| **Ütemezett feladat** | Tevékenységek létrehozása <br> Feladat módosítása <br> Feladat eltávolítása <br> Feladat futtatása |
| **Munkamenetek** | Felhasználó megszemélyesítése <br> Üzenet <br> Munkamenet ellenőrzése <br> Munkamenetek megtekintése és leállítása |
| **Adattár-fürt** | Adattár-fürt konfigurálása |
| **Profil alapú tárolás** | Profil-vezérelt tároló frissítése <br> Profil alapú tárolási nézet |
| **Tárolási nézetek** | Szolgáltatás konfigurálása <br> Megtekintés |
| **Feladatok** | Feladat létrehozása <br> Feladat frissítése |
| **Szállítási szolgáltatás**| Kezelés <br> Figyelés |
| **vApp** | Virtuális gép hozzáadása <br> Erőforráskészlet kiosztása <br> VApp-hozzárendelés <br> Klónozás <br> Létrehozás <br> Törlés <br> Exportálás <br> Importálás <br> Áthelyezés <br> Kikapcsolás <br> Bekapcsolás <br> Átnevezés <br> Felfüggesztheti <br> Regisztrálás törlése <br> OVF-környezet megtekintése <br> vApp-alkalmazás konfigurációja <br> vApp-példány konfigurációja <br> vApp többé-konfiguráció <br> vApp erőforrás-konfiguráció |
| **VRMPolicy** | VRMPolicy lekérdezése <br> VRMPolicy frissítése |
| **Virtuális gép > konfigurációja** | Meglévő lemez hozzáadása <br> Új lemez hozzáadása <br> Eszköz hozzáadása vagy eltávolítása <br> Extra szintű <br> CPU-szám módosítása <br> Erőforrás módosítása <br> Többé konfigurálása <br> Lemez változásának követése <br> Lemez bérlete <br> Kapcsolatbeállítások megjelenítése <br> Virtuális lemez kiterjesztése <br> Gazdagép USB-eszköze <br> Memória <br> Eszközbeállítások módosítása <br> Hibatűrő kompatibilitási lekérdezés <br> A nem birtokolt fájlok lekérdezése <br> Nyers eszköz <br> Újratöltés az elérési útról <br> Lemez eltávolítása <br> Átnevezés <br> Vendég adatainak alaphelyzetbe állítása <br> Jegyzet beállítása <br> Beállítások <br> Swapfile elhelyezése <br> Elágazás szülőjének váltása <br> Virtuális gép feloldása <br> A virtuális gépek kompatibilitásának frissítése |
| **Virtuális gép > vendég műveletei** | Vendég műveleti alias módosítása <br> Vendég műveleti alias lekérdezése <br> Vendég művelet módosításai <br> Vendég műveleti program végrehajtása <br> Vendég műveleti lekérdezések |
| **Virtuális gép > interakció** | Válasz kérdése <br> Biztonsági mentési művelet a virtuális gépen <br> CD-adathordozó konfigurálása <br> Hajlékonylemezes adathordozó konfigurálása <br> Konzol interakciója <br> Képernyőkép létrehozása <br> Az összes lemez töredezettségmentesítése <br> Eszköz csatlakoztatása <br> Drag and drop <br> Vendég operációs rendszer felügyelete a VIX API-val <br> USB-HID-beolvasó kódok behelyezése <br> Szüneteltetés vagy szüneteltetés <br> Törlési vagy zsugorodási műveletek végrehajtása <br> Kikapcsolás <br> Bekapcsolás <br> Munkamenet rögzítése a virtuális gépen <br> Visszajátszás-munkamenet a virtuális gépen <br> Alaphelyzetbe állítás <br> Hibatűrés folytatása <br> Felfüggesztheti <br> Hibatűrés felfüggesztése <br> Feladatátvétel tesztelése <br> Másodlagos virtuális gép újraindításának tesztelése <br> Hibatűrés kikapcsolása <br> Hibatűrés bekapcsolása <br> VMware-eszközök telepítése |
| **Virtuális gép > leltározása** | Létrehozás meglévőből <br> Új létrehozása <br> Áthelyezés <br> Regisztráció <br> Eltávolítás <br> Regisztrálás törlése |
| **Virtuális gép > kiépítés** | Lemezes hozzáférés engedélyezése <br> Fájl-hozzáférés engedélyezése <br> Írásvédett lemezes hozzáférés engedélyezése <br> Virtuális gép letöltésének engedélyezése <br> Virtuális gépek fájljainak feltöltésének engedélyezése <br> Sablon klónozása <br> Virtuális gép klónozása <br> Sablon létrehozása virtuális gépről <br> Testreszabás <br> Sablon üzembe helyezése <br> Megjelölés sablonként <br> Megjelölés virtuális gépnek <br> Testreszabási specifikáció módosítása <br> Lemezek előléptetése <br> Testreszabási specifikációk olvasása |
| **Virtuális gép > szolgáltatás konfigurációja** | Értesítések engedélyezése <br> Globális események értesítéseinek lekérdezésének engedélyezése <br> Szolgáltatási konfigurációk kezelése <br> Szolgáltatás konfigurációjának módosítása <br> Lekérdezési szolgáltatás konfigurációi <br> Szolgáltatás konfigurációjának olvasása |
| **Virtuális gép > Pillanatképek kezelése** | Pillanatkép készítése <br> Pillanatkép eltávolítása <br> Pillanatkép átnevezése <br> Helyreállítás pillanatképre |
| **Virtuális gép > vSphere-replikáció** | Replikáció konfigurálása <br> Replikáció kezelése <br> Replikáció figyelése |
| **vService** | Függőség létrehozása <br> Függőség megsemmisítése <br> Függőség konfigurációjának újrakonfigurálása <br> Frissítési függőség |

### <a name="cloud-cluster-admin-role"></a>Felhő – fürt – rendszergazda – szerepkör

| **Kategória** | **Jogosultság** |
|----------|-----------|
| **Datastore** | Tárterület foglalása <br> Adattár tallózása <br> Adattár konfigurálása <br> Alacsony szintű fájlok műveletei <br> Adattár eltávolítása <br> Adattár átnevezése <br> Virtuálisgép-fájlok frissítése <br> Virtuális gép metaadatainak frissítése |
| **Mappa** | Mappa létrehozása <br> Mappa törlése <br> Mappa áthelyezése <br> Mappa átnevezése |
| **Gazdagép > konfigurálása**  | Tárolási partíció konfigurációja |
| **vSphere címkézése** | VSphere-címke hozzárendelésének vagy hozzárendelésének megszüntetése <br> VSphere címke létrehozása <br> VSphere-címke kategória létrehozása <br> VSphere-címke törlése <br> VSphere törlése <br> VSphere címke szerkesztése <br> VSphere szerkesztése címke kategóriája <br> Kategória UsedBy-mezőjének módosítása <br> Címke UsedBy-mezőjének módosítása |
| **Hálózat** | Hálózat kiosztása |
| **Erőforrás** | Javaslat alkalmazása <br> VApp erőforrás-készlethez rendelése <br> Virtuális gép erőforrás-készlethez rendelése <br> Erőforráskészlet létrehozása <br> Virtuális gép migrálása <br> Migrálás virtuális gépen <br> Erőforráskészlet módosítása <br> Erőforráskészlet áthelyezése <br> VMotion lekérdezése <br> Erőforráskészlet eltávolítása <br> Erőforráskészlet átnevezése |
| **vApp** | Virtuális gép hozzáadása <br> Erőforráskészlet kiosztása <br> VApp-hozzárendelés <br> Klónozás <br> Létrehozás <br> Törlés <br> Exportálás <br> Importálás <br> Áthelyezés <br> Kikapcsolás <br> Bekapcsolás <br> Átnevezés <br> Felfüggesztheti <br> Regisztrálás törlése <br> OVF-környezet megtekintése <br> vApp-alkalmazás konfigurációja <br> vApp-példány konfigurációja <br> vApp többé-konfiguráció <br> vApp erőforrás-konfiguráció |
| **VRMPolicy** | VRMPolicy lekérdezése <br> VRMPolicy frissítése |
| **Virtuális gép > konfigurációja** | Meglévő lemez hozzáadása <br> Új lemez hozzáadása <br> Eszköz hozzáadása vagy eltávolítása <br> Extra szintű <br> CPU-szám módosítása <br> Erőforrás módosítása <br> Többé konfigurálása <br> Lemez változásának követése <br> Lemez bérlete <br> Kapcsolatbeállítások megjelenítése <br> Virtuális lemez kiterjesztése <br> Gazdagép USB-eszköze <br> Memória <br> Eszközbeállítások módosítása <br> Hibatűrő kompatibilitási lekérdezés <br> A nem birtokolt fájlok lekérdezése <br> Nyers eszköz <br> Újratöltés az elérési útról <br> Lemez eltávolítása <br> Átnevezés <br> Vendég adatainak alaphelyzetbe állítása <br> Jegyzet beállítása <br> Beállítások <br> Swapfile elhelyezése <br> Elágazás szülőjének váltása <br> Virtuális gép feloldása <br> A virtuális gépek kompatibilitásának frissítése |
| **Virtuális gép > vendég műveletei** | Vendég műveleti alias módosítása <br> Vendég műveleti alias lekérdezése <br> Vendég művelet módosításai <br> Vendég műveleti program végrehajtása <br> Vendég műveleti lekérdezések |
| **Virtuális gép > interakció** | Válasz kérdése <br> Biztonsági mentési művelet a virtuális gépen <br> CD-adathordozó konfigurálása <br> Hajlékonylemezes adathordozó konfigurálása <br> Konzol interakciója <br> Képernyőkép létrehozása <br> Az összes lemez töredezettségmentesítése <br> Eszköz csatlakoztatása <br> Drag and drop <br> Vendég operációs rendszer felügyelete a VIX API-val <br> USB-HID-beolvasó kódok behelyezése <br> Szüneteltetés vagy szüneteltetés <br> Törlési vagy zsugorodási műveletek végrehajtása <br> Kikapcsolás <br> Bekapcsolás <br> Munkamenet rögzítése a virtuális gépen <br> Visszajátszás-munkamenet a virtuális gépen <br> Alaphelyzetbe állítás <br> Hibatűrés folytatása <br> Felfüggesztheti <br> Hibatűrés felfüggesztése <br> Feladatátvétel tesztelése <br> Másodlagos virtuális gép újraindításának tesztelése <br> Hibatűrés kikapcsolása <br> Hibatűrés bekapcsolása <br> VMware-eszközök telepítése
| **Virtuális gép > leltározása** | Létrehozás meglévőből <br> Új létrehozása <br> Áthelyezés <br> Regisztráció <br> Eltávolítás <br> Regisztrálás törlése |
| **Virtuális gép > kiépítés** | Lemezes hozzáférés engedélyezése <br> Fájl-hozzáférés engedélyezése <br> Írásvédett lemezes hozzáférés engedélyezése <br> Virtuális gép letöltésének engedélyezése <br> Virtuális gépek fájljainak feltöltésének engedélyezése <br> Sablon klónozása <br> Virtuális gép klónozása <br> Sablon létrehozása virtuális gépről <br> Testreszabás <br> Sablon üzembe helyezése <br> Megjelölés sablonként <br> Megjelölés virtuális gépnek <br> Testreszabási specifikáció módosítása <br> Lemezek előléptetése  <br> Testreszabási specifikációk olvasása |
| **Virtuális gép > szolgáltatás konfigurációja** | Értesítések engedélyezése <br> Globális események értesítéseinek lekérdezésének engedélyezése <br> Szolgáltatási konfigurációk kezelése <br> Szolgáltatás konfigurációjának módosítása <br> Lekérdezési szolgáltatás konfigurációi <br> Szolgáltatás konfigurációjának olvasása
| **Virtuális gép > Pillanatképek kezelése** | Pillanatkép készítése <br> Pillanatkép eltávolítása <br> Pillanatkép átnevezése <br> Helyreállítás pillanatképre |
| **Virtuális gép > vSphere-replikáció** | Replikáció konfigurálása <br> Replikáció kezelése <br> Replikáció figyelése |
| **vService** | Függőség létrehozása <br> Függőség megsemmisítése <br> Függőség konfigurációjának újrakonfigurálása <br> Frissítési függőség |

### <a name="cloud-storage-admin-role"></a>Felhőalapú tárolás – rendszergazda – szerepkör

| **Kategória** | **Jogosultság** |
|----------|-----------|
| **Datastore** | Tárterület foglalása <br> Adattár tallózása <br> Adattár konfigurálása <br> Alacsony szintű fájlok műveletei <br> Adattár eltávolítása <br> Adattár átnevezése <br> Virtuálisgép-fájlok frissítése <br> Virtuális gép metaadatainak frissítése |
| **Gazdagép > konfigurálása** | Tárolási partíció konfigurációja |
| **Adattár-fürt** | Adattár-fürt konfigurálása |
| **Profil alapú tárolás** | Profil-vezérelt tároló frissítése <br> Profil alapú tárolási nézet |
| **Tárolási nézetek** | Szolgáltatás konfigurálása <br> Megtekintés |

### <a name="cloud-network-admin-role"></a>Cloud-Network-admin-role

| **Kategória** | **Jogosultság** |
|----------|-----------|
| **dvPort csoport** | Létrehozás <br> Törlés <br> Módosítás <br> Házirend-művelet <br> Hatókör-művelet |
| **Hálózat** | Hálózat kiosztása <br> Konfigurálás <br> Hálózat áthelyezése <br> Eltávolítás |
| **Virtuális gép > konfigurációja** | Eszközbeállítások módosítása |

### <a name="cloud-vm-admin-role"></a>Cloud-VM-admin-role

| **Kategória** | **Jogosultság** |
|----------|-----------|
| **Datastore** | Tárterület foglalása <br> Adattár tallózása |
| **Hálózat** | Hálózat kiosztása |
| **Erőforrás** | Virtuális gép erőforrás-készlethez rendelése <br> Virtuális gép migrálása <br> Migrálás virtuális gépen
| **vApp** | Exportálás <br> Importálás |
| **Virtuális gép > konfigurációja** | Meglévő lemez hozzáadása <br> Új lemez hozzáadása <br> Eszköz hozzáadása vagy eltávolítása <br> Extra szintű <br> CPU-szám módosítása <br> Erőforrás módosítása <br> Többé konfigurálása <br> Lemez változásának követése <br> Lemez bérlete <br> Kapcsolatbeállítások megjelenítése <br> Virtuális lemez kiterjesztése <br> Gazdagép USB-eszköze <br> Memória <br> Eszközbeállítások módosítása <br> Hibatűrő kompatibilitási lekérdezés <br> A nem birtokolt fájlok lekérdezése <br> Nyers eszköz <br> Újratöltés az elérési útról <br> Lemez eltávolítása <br> Átnevezés <br> Vendég adatainak alaphelyzetbe állítása <br> Jegyzet beállítása <br> Beállítások <br> Swapfile elhelyezése <br> Elágazás szülőjének váltása <br> Virtuális gép feloldása <br> A virtuális gépek kompatibilitásának frissítése |
| **Virtuális gép > vendég műveletei** | Vendég műveleti alias módosítása <br> Vendég műveleti alias lekérdezése <br> Vendég művelet módosításai <br> Vendég műveleti program végrehajtása <br> Vendég műveleti lekérdezések    |
| **Virtuális gép > interakció** | Válasz kérdése <br> Biztonsági mentési művelet a virtuális gépen <br> CD-adathordozó konfigurálása <br> Hajlékonylemezes adathordozó konfigurálása <br> Konzol interakciója <br> Képernyőkép létrehozása <br> Az összes lemez töredezettségmentesítése <br> Eszköz csatlakoztatása <br> Drag and drop <br> Vendég operációs rendszer felügyelete a VIX API-val <br> USB-HID-beolvasó kódok behelyezése <br> Szüneteltetés vagy szüneteltetés <br> Törlési vagy zsugorodási műveletek végrehajtása <br> Kikapcsolás <br> Bekapcsolás <br> Munkamenet rögzítése a virtuális gépen <br> Visszajátszás-munkamenet a virtuális gépen <br> Alaphelyzetbe állítás <br> Hibatűrés folytatása <br> Felfüggesztheti <br> Hibatűrés felfüggesztése <br> Feladatátvétel tesztelése <br> Másodlagos virtuális gép újraindításának tesztelése <br> Hibatűrés kikapcsolása <br> Hibatűrés bekapcsolása <br> VMware-eszközök telepítése |
| **Virtuális gép > leltározása** | Létrehozás meglévőből <br> Új létrehozása <br> Áthelyezés <br> Regisztráció <br> Eltávolítás <br> Regisztrálás törlése |
| **Virtuális gép > kiépítés** | Lemezes hozzáférés engedélyezése <br> Fájl-hozzáférés engedélyezése <br> Írásvédett lemezes hozzáférés engedélyezése <br> Virtuális gép letöltésének engedélyezése <br> Virtuális gépek fájljainak feltöltésének engedélyezése <br> Sablon klónozása <br> Virtuális gép klónozása <br> Sablon létrehozása virtuális gépről <br> Testreszabás <br> Sablon üzembe helyezése <br> Megjelölés sablonként <br> Megjelölés virtuális gépnek <br> Testreszabási specifikáció módosítása <br> Lemezek előléptetése <br> Testreszabási specifikációk olvasása |
| **Virtuális gép > szolgáltatás konfigurációja** | Értesítések engedélyezése <br> Globális események értesítéseinek lekérdezésének engedélyezése <br> Szolgáltatási konfigurációk kezelése <br> Szolgáltatás konfigurációjának módosítása <br> Lekérdezési szolgáltatás konfigurációi <br> Szolgáltatás konfigurációjának olvasása
| **Virtuális gép > Pillanatképek kezelése** | Pillanatkép készítése <br> Pillanatkép eltávolítása <br> Pillanatkép átnevezése <br> Helyreállítás pillanatképre |
| **Virtuális gép > vSphere-replikáció** | Replikáció konfigurálása <br> Replikáció kezelése <br> Replikáció figyelése |
| **vService** | Függőség létrehozása <br> Függőség megsemmisítése <br> Függőség konfigurációjának újrakonfigurálása <br> Frissítési függőség |
