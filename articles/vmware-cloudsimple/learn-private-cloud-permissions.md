---
title: Azure VMware-megoldás a CloudSimple által – privát felhőengedély-modell
description: A CloudSimple private cloud engedélymodell, csoportok és kategóriák ismertetése
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 28c4dc7831f97d66eb4d47f08e640344d5cca0d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014946"
---
# <a name="cloudsimple-private-cloud-permission-model-of-vmware-vcenter"></a>A VMware vCenter CloudSimple magánfelhő-engedélyezési modellje

A CloudSimple teljes körű rendszergazdai hozzáférést biztosít a privát felhőkörnyezethez. Minden CloudSimple-ügyfél megfelelő rendszergazdai jogosultsággal rendelkezik ahhoz, hogy a környezetében telepíthesd és kezelhesd a virtuális gépeket.  Szükség esetén ideiglenesen elévülhet a felügyeleti funkciók elvégzéséhez szükséges jogosultságai.

## <a name="cloud-owner"></a>Felhőtulajdonosa

Amikor létrehoz egy magánfelhőt, egy **CloudOwner** felhasználó jön létre a vCenter Single Sign-On tartományban, **a felhő-tulajdonos-szerepkör** hozzáféréssel az objektumok kezeléséhez a magánfelhőben. Ez a felhasználó további [vCenter-identitásforrásokat](set-vcenter-identity.md)és más felhasználókat is beállíthat a Private Cloud vCenter-be.

> [!NOTE]
> A CloudSimple Private Cloud vCenter cloudowner@cloudsimple.local alapértelmezett felhasználója a magánfelhő létrehozásakor van.

## <a name="user-groups"></a>Felhasználói csoportok

A **felhő-tulajdonos-csoport** nevű csoport jön létre a magánfelhő üzembe helyezése során. Ebben a csoportban a felhasználók a vSphere környezet különböző részeit felügyelhetik a privát felhőben. Ez a csoport automatikusan megkapja a **felhő-tulajdonos-szerepkör** jogosultságokat, és a **CloudOwner** felhasználó a csoport tagjaként kerül hozzáadásra.  A CloudSimple további csoportokat hoz létre korlátozott jogosultságokkal a kezelés megkönnyítése érdekében.  Bármely felhasználót hozzáadhat ezekhez az előre létrehozott csoportokhoz, és az alábbiakban meghatározott jogosultságok automatikusan hozzá vannak rendelve a csoportok felhasználóihoz.

### <a name="pre-created-groups"></a>Előre létrehozott csoportok

| Csoportnév | Cél | Szerepkör |
| -------- | ------- | ------ |
| Felhő-tulajdonos-csoport | A csoport tagjai rendszergazdai jogosultságokkal rendelkeznek a Private Cloud vCenter számára | [Felhő-tulajdonos-szerepkör](#cloud-owner-role) |
| Felhő-Global-Cluster-Admin-Csoport | A csoport tagjai rendszergazdai jogosultságokkal rendelkeznek a Private Cloud vCenter-fürtön | [Felhő-fürt-rendszergazdai szerepkör](#cloud-cluster-admin-role) |
| Felhő-Global-Storage-Admin-Csoport | A csoport tagjai kezelhetik a magánfelhő-vCenteren lévő tárhelyet | [Felhő-storage-rendszergazdai szerepkör](#cloud-storage-admin-role) |
| Felhő-Global-Network-Admin-Csoport | A csoport tagjai kezelhetik a hálózati és elosztott portcsoportokat a Private Cloud vCenter szolgáltatásban | [Felhő-hálózat-rendszergazdai szerepkör](#cloud-network-admin-role) |
| Felhő-Global-VM-Admin-Csoport | A csoport tagjai kezelhetik a virtuális gépeket a Private Cloud vCenter | [Felhő-VM-Rendszergazda-szerepkör](#cloud-vm-admin-role) |

Ha az egyes felhasználóknak engedélyt szeretne adni a magánfelhő kezeléséhez, hozzon létre felhasználói fiókokat a megfelelő csoportokhoz.

> [!CAUTION]
> Új felhasználókat csak a *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* vagy *cloud-global-vm-admin-group csoporthoz*kell hozzáadni.  A *Rendszergazdák* csoportba hozzáadott felhasználók automatikusan törlődnek.  Csak a szolgáltatásfiókokat kell hozzáadni *a Rendszergazdák* csoporthoz, és a szolgáltatásfiókok nem használhatók a vSphere webes felhasználói felületére való bejelentkezéshez.

## <a name="list-of-vcenter-privileges-for-default-roles"></a>Az alapértelmezett szerepkörök vCenter-jogosultságainak listája

### <a name="cloud-owner-role"></a>Felhő-tulajdonos-szerepkör

| **Kategória** | **Privilege** |
|----------|-----------|
| **Riasztások** | Riasztás nyugtázása <br> Riasztás létrehozása <br> Riasztási művelet letiltása <br> Riasztás módosítása <br> Riasztás eltávolítása <br> Riasztás állapotának beállítása |
| **Engedélyek** | Engedély módosítása |
| **Tartalomtár** | Tárelem hozzáadása <br> Helyi tár létrehozása <br> Előfizetett tár létrehozása <br> Tárelem törlése <br> Helyi könyvtár törlése <br> Előfizetett tár törlése <br> Fájlok letöltése <br> Tárelem kizárása <br> Előfizetett könyvtár kizárása <br> Tárolás importálása <br> Előfizetési adatok vizsgálata <br> Olvasási tárolás <br> Tárelem szinkronizálása <br> Előfizetett tár szinkronizálása <br> Beírás befelé <br> Konfigurációs beállítások frissítése <br> Fájlok frissítése <br> Tár frissítése <br> Tárelem frissítése <br> Helyi könyvtár frissítése <br> Előfizetett könyvtár frissítése <br> Konfigurációs beállítások megtekintése |
| **Kriptográfiai műveletek** | Lemez hozzáadása <br> Klónozás <br> Visszafejtés <br> Direct Access <br> Titkosítás <br> Új titkosítása <br> KMS kezelése <br> Titkosítási házirendek kezelése <br> Kulcsok kezelése <br> Migrate (Áttelepítés) <br> Újratitkosítás <br> Virtuális gép regisztrálása <br> Állomás regisztrálása |
| **dvPort csoport** | Létrehozás <br> Törlés <br> Módosítás <br> Házirend-művelet <br> Hatókör művelete |
| **Adattár** | Hely lefoglalása <br> Adattár böngészése <br> Adattár konfigurálása <br> Alacsony szintű fájlműveletek <br> Adattár áthelyezése <br> Adattár eltávolítása <br> Fájl eltávolítása <br> Adattár átnevezése <br> Virtuálisgép-fájlok frissítése <br> A virtuális gép metaadatainak frissítése |
| **ESX ügynökkezelő** | Konfigurálás <br> Módosítás <br> Nézet |
| **Mellék** | Kiterjesztés regisztrálása <br> Bővítmény regisztrációjának megszüntetése <br> Bővítmény frissítése |
| **Külső statisztika szolgáltató**| Regisztráljon <br> Regisztrálás törlése <br> Frissítés |
| **Mappa** | Mappa létrehozása <br> Mappa törlése <br> Mappa áthelyezése <br> Mappa átnevezése |
| **Globális** | Feladat megszakítása <br> Kapacitástervezés <br> Diagnosztika <br> Metódusok letiltása <br> Metódusok engedélyezése <br> Globális címke <br> Állapot <br> Licencek <br> Esemény naplózása <br> Egyéni attribútumok kezelése <br> Proxy <br> Parancsfájl-művelet <br> Szolgáltatáskezelők <br> Egyéni attribútum beállítása <br> Rendszercímke |
| **Állapotfrissítés-szolgáltató** | Regisztráljon <br> Regisztrálás törlése <br> Frissítés |
| **Állomás > konfigurációja** | Tárolópartíció-konfiguráció |
| **Állomás > leltár** | Fürt módosítása |
| **vSphere címkézés** | VSphere-címke hozzárendelése vagy visszavonása <br> VSphere címke létrehozása <br> VSphere címkekategória létrehozása <br> VSphere címke törlése <br> VSphere címkekategória törlése <br> Edit vSphere címke <br> Edit vSphere címke kategória <br> A Kategória Használt mezője módosítása <br> A Címke Használt mezője módosítása |
| **Hálózat** | Hálózat hozzárendelése <br> Konfigurálás <br> Hálózat áthelyezése <br> Eltávolítás |
| **Teljesítmény** | Időközök módosítása |
| **Állomásprofil** | Nézet |
| **Erőforrás** | Javaslat alkalmazása <br> VApp hozzárendelése erőforráskészlethez <br> Virtuális gép hozzárendelése erőforráskészlethez <br> Erőforráskészlet létrehozása <br> Kikapcsolt virtuális gép áttelepítése <br> Gépi áttelepítés a virtuális gépen <br> Erőforráskészlet módosítása <br> Erőforráskészlet áthelyezése <br> Lekérdezés vMotion <br> Erőforráskészlet eltávolítása <br> Erőforráskészlet átnevezése |
| **Ütemezett feladat** | Tevékenységek létrehozása <br> Feladat módosítása <br> Feladat eltávolítása <br> Feladat futtatása |
| **Munkamenetek** | Felhasználó megszemélyesítése <br> Üzenet <br> Munkamenet ellenőrzése <br> Munkamenetek megtekintése és leállítása |
| **Adattár-fürt** | Adattárfürt konfigurálása |
| **Profilalapú tárolás** | Profilalapú tárolási frissítés <br> Profilalapú tárolónézet |
| **Tárolási nézetek** | Szolgáltatás konfigurálása <br> Nézet |
| **Feladatok** | Feladat létrehozása <br> Feladat frissítése |
| **Átviteli szolgáltatás**| Kezelés <br> Figyelés |
| **vApp** | Virtuális gép hozzáadása <br> Erőforráskészlet hozzárendelése <br> VApp hozzárendelése <br> Klónozás <br> Létrehozás <br> Törlés <br> Exportálás <br> Importálás <br> Áthelyezés <br> Kikapcsolás <br> Bekapcsolás <br> Átnevezés <br> Felfüggesztés <br> Regisztrálás törlése <br> OVF-környezet megtekintése <br> vApp alkalmazás konfigurációja <br> vApp-példány konfigurációja <br> vApp managedby konfiguráció <br> vApp erőforrás-konfiguráció |
| **VRM-irányelv** | VRMPolicy lekérdezése <br> VRMPolicy frissítése |
| **Virtuális gép > konfigurációja** | Meglévő lemez hozzáadása <br> Új lemez hozzáadása <br> Eszköz hozzáadása vagy eltávolítása <br> Speciális <br> A PROCESSZOR-szám módosítása <br> Erőforrás módosítása <br> Managed managedby (felügyelt) konfigurálása <br> Lemezváltozások nyomon követése <br> Lemezbérlet <br> Kapcsolatbeállítások megjelenítése <br> Virtuális lemez bővítése <br> USB-eszköz gazdagépe <br> Memory (Memória) <br> Eszközbeállítások módosítása <br> Lekérdezési hibatűvel való kompatibilitás <br> Nem birtokolt fájlok lekérdezése <br> Nyers eszköz <br> Újratöltés az elérési útról <br> Lemez eltávolítása <br> Átnevezés <br> Vendégadatok alaphelyzetbe állítása <br> Jegyzet beállítása <br> Beállítások <br> Felcserélő fájl elhelyezése <br> Villa szülőváltása <br> Virtuális gép zárolásának feloldása <br> A virtuális gépek kompatibilitásának frissítése |
| **Virtuális gép > vendég műveletek** | Vendég művelet alias módosítása <br> Vendégművelet alias lekérdezése <br> Vendég művelet módosításai <br> Vendég műveleti program végrehajtása <br> Vendégműveleti lekérdezések |
| **Virtuális gép > interakció** | Válasz kérdés <br> Biztonsági mentési művelet a virtuális gépen <br> CD-adathordozó konfigurálása <br> Hajlékonylemez-adathordozó konfigurálása <br> Konzollal való interakció <br> Képernyőkép létrehozása <br> Az összes lemez töredezettségmentesítése <br> Eszközkapcsolat <br> Húzás <br> Vendég operációs rendszer kezelése VIX API-val <br> USB HID-beési kódok befecskendezése <br> Szüneteltetés vagy szüneteltetés <br> Törlési vagy zsugorítási műveletek végrehajtása <br> Kikapcsolás <br> Bekapcsolás <br> Munkamenet rögzítése a virtuális gépen <br> Visszajátszási munkamenet a virtuális gépen <br> Alaphelyzetbe állítás <br> Folytatási hibatűrést <br> Felfüggesztés <br> Hibatűrés felfüggesztése <br> Feladatátvétel tesztelése <br> Teszt újraindításmásodlagos virtuális gép <br> Hibatűrés kikapcsolása <br> Hibatűrés bekapcsolása <br> VMware eszközök telepítése |
| **Virtuális gép > leltár** | Létrehozás meglévő <br> Új létrehozása <br> Áthelyezés <br> Regisztráljon <br> Eltávolítás <br> Regisztrálás törlése |
| **Virtuális gép > kiépítés** | Lemezhozzáférés engedélyezése <br> Fájlhozzáférés engedélyezése <br> Írásvédett lemezhozzáférés engedélyezése <br> Virtuális gép letöltésének engedélyezése <br> Virtuálisgép-fájlok feltöltésének engedélyezése <br> Klónozott sablon <br> Virtuális gép klónozása <br> Sablon létrehozása virtuális gépről <br> Testreszabás <br> Sablon üzembe helyezése <br> Megjelölés sablonként <br> Megjelölés virtuális gépként <br> Testreszabási specifikáció módosítása <br> Lemezek előléptetése <br> Testreszabási specifikációk olvasása |
| **Virtuális gép > szolgáltatás konfigurációja** | Értesítések engedélyezése <br> Globális eseményértesítések lekérdezésének engedélyezése <br> Szolgáltatáskonfigurációk kezelése <br> Szolgáltatáskonfiguráció módosítása <br> Szolgáltatáskonfigurációk lekérdezése <br> Szolgáltatás konfigurációjának olvasása |
| **Virtuális gép > pillanatkép-kezelés** | Pillanatkép készítése <br> Pillanatkép eltávolítása <br> Pillanatkép átnevezése <br> Visszaállítás a pillanatképhez |
| **Virtuális gép > vSphere replikáció** | Replikáció konfigurálása <br> Replikáció kezelése <br> Replikáció monitorozása |
| **vSzolgáltatás** | Függőség létrehozása <br> Függőség megsemmisítése <br> Függőségi konfiguráció újrakonfigurálása <br> Függőség frissítése |

### <a name="cloud-cluster-admin-role"></a>Felhő-fürt-rendszergazdai szerepkör

| **Kategória** | **Privilege** |
|----------|-----------|
| **Adattár** | Hely lefoglalása <br> Adattár böngészése <br> Adattár konfigurálása <br> Alacsony szintű fájlműveletek <br> Adattár eltávolítása <br> Adattár átnevezése <br> Virtuálisgép-fájlok frissítése <br> A virtuális gép metaadatainak frissítése |
| **Mappa** | Mappa létrehozása <br> Mappa törlése <br> Mappa áthelyezése <br> Mappa átnevezése |
| **Állomás > konfigurációja**  | Tárolópartíció-konfiguráció |
| **vSphere címkézés** | VSphere-címke hozzárendelése vagy visszavonása <br> VSphere címke létrehozása <br> VSphere címkekategória létrehozása <br> VSphere címke törlése <br> VSphere címkekategória törlése <br> Edit vSphere címke <br> Edit vSphere címke kategória <br> A Kategória Használt mezője módosítása <br> A Címke Használt mezője módosítása |
| **Hálózat** | Hálózat hozzárendelése |
| **Erőforrás** | Javaslat alkalmazása <br> VApp hozzárendelése erőforráskészlethez <br> Virtuális gép hozzárendelése erőforráskészlethez <br> Erőforráskészlet létrehozása <br> Kikapcsolt virtuális gép áttelepítése <br> Gépi áttelepítés a virtuális gépen <br> Erőforráskészlet módosítása <br> Erőforráskészlet áthelyezése <br> Lekérdezés vMotion <br> Erőforráskészlet eltávolítása <br> Erőforráskészlet átnevezése |
| **vApp** | Virtuális gép hozzáadása <br> Erőforráskészlet hozzárendelése <br> VApp hozzárendelése <br> Klónozás <br> Létrehozás <br> Törlés <br> Exportálás <br> Importálás <br> Áthelyezés <br> Kikapcsolás <br> Bekapcsolás <br> Átnevezés <br> Felfüggesztés <br> Regisztrálás törlése <br> OVF-környezet megtekintése <br> vApp alkalmazás konfigurációja <br> vApp-példány konfigurációja <br> vApp managedby konfiguráció <br> vApp erőforrás-konfiguráció |
| **VRM-irányelv** | VRMPolicy lekérdezése <br> VRMPolicy frissítése |
| **Virtuális gép > konfigurációja** | Meglévő lemez hozzáadása <br> Új lemez hozzáadása <br> Eszköz hozzáadása vagy eltávolítása <br> Speciális <br> A PROCESSZOR-szám módosítása <br> Erőforrás módosítása <br> Managed managedby (felügyelt) konfigurálása <br> Lemezváltozások nyomon követése <br> Lemezbérlet <br> Kapcsolatbeállítások megjelenítése <br> Virtuális lemez bővítése <br> USB-eszköz gazdagépe <br> Memory (Memória) <br> Eszközbeállítások módosítása <br> Lekérdezési hibatűvel való kompatibilitás <br> Nem birtokolt fájlok lekérdezése <br> Nyers eszköz <br> Újratöltés az elérési útról <br> Lemez eltávolítása <br> Átnevezés <br> Vendégadatok alaphelyzetbe állítása <br> Jegyzet beállítása <br> Beállítások <br> Felcserélő fájl elhelyezése <br> Villa szülőváltása <br> Virtuális gép zárolásának feloldása <br> A virtuális gépek kompatibilitásának frissítése |
| **Virtuális gép > vendég műveletek** | Vendég művelet alias módosítása <br> Vendégművelet alias lekérdezése <br> Vendég művelet módosításai <br> Vendég műveleti program végrehajtása <br> Vendégműveleti lekérdezések |
| **Virtuális gép > interakció** | Válasz kérdés <br> Biztonsági mentési művelet a virtuális gépen <br> CD-adathordozó konfigurálása <br> Hajlékonylemez-adathordozó konfigurálása <br> Konzollal való interakció <br> Képernyőkép létrehozása <br> Az összes lemez töredezettségmentesítése <br> Eszközkapcsolat <br> Húzás <br> Vendég operációs rendszer kezelése VIX API-val <br> USB HID-beési kódok befecskendezése <br> Szüneteltetés vagy szüneteltetés <br> Törlési vagy zsugorítási műveletek végrehajtása <br> Kikapcsolás <br> Bekapcsolás <br> Munkamenet rögzítése a virtuális gépen <br> Visszajátszási munkamenet a virtuális gépen <br> Alaphelyzetbe állítás <br> Folytatási hibatűrést <br> Felfüggesztés <br> Hibatűrés felfüggesztése <br> Feladatátvétel tesztelése <br> Teszt újraindításmásodlagos virtuális gép <br> Hibatűrés kikapcsolása <br> Hibatűrés bekapcsolása <br> VMware eszközök telepítése
| **Virtuális gép > leltár** | Létrehozás meglévő <br> Új létrehozása <br> Áthelyezés <br> Regisztráljon <br> Eltávolítás <br> Regisztrálás törlése |
| **Virtuális gép > kiépítés** | Lemezhozzáférés engedélyezése <br> Fájlhozzáférés engedélyezése <br> Írásvédett lemezhozzáférés engedélyezése <br> Virtuális gép letöltésének engedélyezése <br> Virtuálisgép-fájlok feltöltésének engedélyezése <br> Klónozott sablon <br> Virtuális gép klónozása <br> Sablon létrehozása virtuális gépről <br> Testreszabás <br> Sablon üzembe helyezése <br> Megjelölés sablonként <br> Megjelölés virtuális gépként <br> Testreszabási specifikáció módosítása <br> Lemezek előléptetése  <br> Testreszabási specifikációk olvasása |
| **Virtuális gép > szolgáltatás konfigurációja** | Értesítések engedélyezése <br> Globális eseményértesítések lekérdezésének engedélyezése <br> Szolgáltatáskonfigurációk kezelése <br> Szolgáltatáskonfiguráció módosítása <br> Szolgáltatáskonfigurációk lekérdezése <br> Szolgáltatás konfigurációjának olvasása
| **Virtuális gép > pillanatkép-kezelés** | Pillanatkép készítése <br> Pillanatkép eltávolítása <br> Pillanatkép átnevezése <br> Visszaállítás a pillanatképhez |
| **Virtuális gép > vSphere replikáció** | Replikáció konfigurálása <br> Replikáció kezelése <br> Replikáció monitorozása |
| **vSzolgáltatás** | Függőség létrehozása <br> Függőség megsemmisítése <br> Függőségi konfiguráció újrakonfigurálása <br> Függőség frissítése |

### <a name="cloud-storage-admin-role"></a>Felhő-storage-rendszergazdai szerepkör

| **Kategória** | **Privilege** |
|----------|-----------|
| **Adattár** | Hely lefoglalása <br> Adattár böngészése <br> Adattár konfigurálása <br> Alacsony szintű fájlműveletek <br> Adattár eltávolítása <br> Adattár átnevezése <br> Virtuálisgép-fájlok frissítése <br> A virtuális gép metaadatainak frissítése |
| **Állomás > konfigurációja** | Tárolópartíció-konfiguráció |
| **Adattár-fürt** | Adattárfürt konfigurálása |
| **Profilalapú tárolás** | Profilalapú tárolási frissítés <br> Profilalapú tárolónézet |
| **Tárolási nézetek** | Szolgáltatás konfigurálása <br> Nézet |

### <a name="cloud-network-admin-role"></a>Felhő-hálózat-rendszergazdai szerepkör

| **Kategória** | **Privilege** |
|----------|-----------|
| **dvPort csoport** | Létrehozás <br> Törlés <br> Módosítás <br> Házirend-művelet <br> Hatókör művelete |
| **Hálózat** | Hálózat hozzárendelése <br> Konfigurálás <br> Hálózat áthelyezése <br> Eltávolítás |
| **Virtuális gép > konfigurációja** | Eszközbeállítások módosítása |

### <a name="cloud-vm-admin-role"></a>Felhő-VM-Rendszergazda-szerepkör

| **Kategória** | **Privilege** |
|----------|-----------|
| **Adattár** | Hely lefoglalása <br> Adattár böngészése |
| **Hálózat** | Hálózat hozzárendelése |
| **Erőforrás** | Virtuális gép hozzárendelése erőforráskészlethez <br> Kikapcsolt virtuális gép áttelepítése <br> Gépi áttelepítés a virtuális gépen
| **vApp** | Exportálás <br> Importálás |
| **Virtuális gép > konfigurációja** | Meglévő lemez hozzáadása <br> Új lemez hozzáadása <br> Eszköz hozzáadása vagy eltávolítása <br> Speciális <br> A PROCESSZOR-szám módosítása <br> Erőforrás módosítása <br> Managed managedby (felügyelt) konfigurálása <br> Lemezváltozások nyomon követése <br> Lemezbérlet <br> Kapcsolatbeállítások megjelenítése <br> Virtuális lemez bővítése <br> USB-eszköz gazdagépe <br> Memory (Memória) <br> Eszközbeállítások módosítása <br> Lekérdezési hibatűvel való kompatibilitás <br> Nem birtokolt fájlok lekérdezése <br> Nyers eszköz <br> Újratöltés az elérési útról <br> Lemez eltávolítása <br> Átnevezés <br> Vendégadatok alaphelyzetbe állítása <br> Jegyzet beállítása <br> Beállítások <br> Felcserélő fájl elhelyezése <br> Villa szülőváltása <br> Virtuális gép zárolásának feloldása <br> A virtuális gépek kompatibilitásának frissítése |
| **Virtuális gép >vendég műveletek** | Vendég művelet alias módosítása <br> Vendégművelet alias lekérdezése <br> Vendég művelet módosításai <br> Vendég műveleti program végrehajtása <br> Vendégműveleti lekérdezések    |
| **Virtuális gép >interakció** | Válasz kérdés <br> Biztonsági mentési művelet a virtuális gépen <br> CD-adathordozó konfigurálása <br> Hajlékonylemez-adathordozó konfigurálása <br> Konzollal való interakció <br> Képernyőkép létrehozása <br> Az összes lemez töredezettségmentesítése <br> Eszközkapcsolat <br> Húzás <br> Vendég operációs rendszer kezelése VIX API-val <br> USB HID-beési kódok befecskendezése <br> Szüneteltetés vagy szüneteltetés <br> Törlési vagy zsugorítási műveletek végrehajtása <br> Kikapcsolás <br> Bekapcsolás <br> Munkamenet rögzítése a virtuális gépen <br> Visszajátszási munkamenet a virtuális gépen <br> Alaphelyzetbe állítás <br> Folytatási hibatűrést <br> Felfüggesztés <br> Hibatűrés felfüggesztése <br> Feladatátvétel tesztelése <br> Teszt újraindításmásodlagos virtuális gép <br> Hibatűrés kikapcsolása <br> Hibatűrés bekapcsolása <br> VMware eszközök telepítése |
| **Virtuális gép >leltár** | Létrehozás meglévő <br> Új létrehozása <br> Áthelyezés <br> Regisztráljon <br> Eltávolítás <br> Regisztrálás törlése |
| **Virtuális gép >kiépítés** | Lemezhozzáférés engedélyezése <br> Fájlhozzáférés engedélyezése <br> Írásvédett lemezhozzáférés engedélyezése <br> Virtuális gép letöltésének engedélyezése <br> Virtuálisgép-fájlok feltöltésének engedélyezése <br> Klónozott sablon <br> Virtuális gép klónozása <br> Sablon létrehozása virtuális gépről <br> Testreszabás <br> Sablon üzembe helyezése <br> Megjelölés sablonként <br> Megjelölés virtuális gépként <br> Testreszabási specifikáció módosítása <br> Lemezek előléptetése <br> Testreszabási specifikációk olvasása |
| **Virtuális gép >szolgáltatás konfigurációja** | Értesítések engedélyezése <br> Globális eseményértesítések lekérdezésének engedélyezése <br> Szolgáltatáskonfigurációk kezelése <br> Szolgáltatáskonfiguráció módosítása <br> Szolgáltatáskonfigurációk lekérdezése <br> Szolgáltatás konfigurációjának olvasása
| **Virtuális gép >pillanatkép-kezelés** | Pillanatkép készítése <br> Pillanatkép eltávolítása <br> Pillanatkép átnevezése <br> Visszaállítás a pillanatképhez |
| **Virtuális gép >vSphere replikáció** | Replikáció konfigurálása <br> Replikáció kezelése <br> Replikáció monitorozása |
| **vSzolgáltatás** | Függőség létrehozása <br> Függőség megsemmisítése <br> Függőségi konfiguráció újrakonfigurálása <br> Függőség frissítése |
