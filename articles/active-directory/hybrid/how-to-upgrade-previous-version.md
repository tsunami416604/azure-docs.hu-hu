---
title: 'Azure AD Connect: Frissítés egy korábbi verzióról | Microsoft dokumentumok'
description: Az Azure Active Directory Connect legújabb kiadására való frissítés különböző módszereit ismerteti, beleértve a helyszíni frissítést és a lengésáttelepítést.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 04/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a3e7373a8b0354a3d08debf944f2f77f1609382
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347691"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: frissítés egy előző verzióról a legújabbra
Ez a témakör ismerteti a különböző módszerek, amelyek segítségével frissítheti az Azure Active Directory (Azure AD) Connect telepítése a legújabb kiadásra. Azt javasoljuk, hogy tartsa magát naprakészen az Azure AD Connect kiadásaival. A Swing áttelepítési szakaszlépéseit is [használhatja,](#swing-migration) ha jelentős konfigurációmódosítást eszközz.

>[!NOTE]
> Jelenleg támogatott az Azure AD Connect bármely verziójáról való frissítés az aktuális verzióra. A DirSync vagy az ADSync helyben történő frissítése nem támogatott, és swing-áttelepítésre van szükség.  Ha a DirSync szolgáltatásról szeretne frissíteni, olvassa el a [Frissítés az Azure AD szinkronizálási eszközről (DirSync)](how-to-dirsync-upgrade-get-started.md) vagy a [Swing-áttelepítés](#swing-migration) i.  </br>A gyakorlatban a rendkívül régi verziókon lévő ügyfelek problémákat tapasztalhatnak, amelyek nem kapcsolódnak közvetlenül az Azure AD Connecthez. Azok a kiszolgálók, amelyek már több éve élestermelésben vannak, jellemzően több javítást alkalmaztak rájuk, és ezek közül nem mindegyik számolható el.  Általában, vásárlók ki volna nem felminősített -ban 12-18 hónapok kellet volna tekint egy hinta feljavít helyett mint ez a leg--bb konzervatív és a legkevésbé kockázatos választás.

Ha a DirSync szolgáltatásról szeretne frissíteni, olvassa el a [Frissítés az Azure AD szinkronizálási eszközről (DirSync)](how-to-dirsync-upgrade-get-started.md) című témakört.

Az Azure AD Connect frissítéséhez néhány különböző stratégia használható.

| Módszer | Leírás |
| --- | --- |
| [Automatikus frissítés](how-to-connect-install-automatic-upgrade.md) |Ez a legegyszerűbb módszer az expressz telepítéssel rendelkező ügyfelek számára. |
| [Frissítés helyben](#in-place-upgrade) |Ha egyetlen kiszolgálóval rendelkezik, a telepítést helyben frissítheti ugyanazon a kiszolgálón. |
| [Párhuzamos migrálás](#swing-migration) |Két kiszolgáló esetén előkészítheti az egyik kiszolgálót az új kiadással vagy konfigurációval, és módosíthatja az aktív kiszolgálót, ha készen áll. |

Az engedélyekkel kapcsolatos információkat a [frissítéshez szükséges engedélyek](reference-connect-accounts-permissions.md#upgrade)ben talál.

> [!NOTE]
> Miután engedélyezte az új Azure AD Connect-kiszolgáló számára a módosítások szinkronizálását az Azure AD-hez, nem állhat vissza a DirSync vagy az Azure AD Sync használatára. Az Azure AD Connectről az örökölt ügyfelekre, például a DirSync-re és az Azure AD Sync-re való visszaminősítés nem támogatott, és olyan problémákhoz vezethet, mint például az Azure AD-ben az adatvesztés.

## <a name="in-place-upgrade"></a>Frissítés helyben
A helybeni frissítés az Azure AD Sync vagy az Azure AD Connect szolgáltatásból való áttéréshez is működik. Nem működik a DirSync-ből való áthelyezéshez vagy a Forefront Identity Manager (FIM) + Azure AD Connector megoldásaihoz.

Ez a módszer akkor ajánlott, ha egyetlen kiszolgálóval rendelkezik, és kevesebb mint 100 000 objektummal rendelkezik. Ha a beépített szinkronizálási szabályok módosulnak, a frissítés után teljes importálás és teljes szinkronizálás történik. Ez a módszer biztosítja, hogy az új konfiguráció a rendszer összes meglévő objektumára vonatkozik. Ez a futtatás eltarthat néhány órát, attól függően, hogy hány objektum van a szinkronizálási motor hatókörében. A normál különbözeti szinkronizálási ütemező (amely alapértelmezés szerint 30 percenként szinkronizál) felfüggesztésre kerül, de a jelszó-szinkronizálás folytatódik. Érdemes lehet a helyben i upgrade-et a hétvégén. Ha az azure-beli AD Connect új kiadásával nincsenek módosítások a beépített konfigurációban, akkor helyette egy normál különbözeti importálás/-szinkronizálás indul el.  
![Frissítés helyben](./media/how-to-upgrade-previous-version/inplaceupgrade.png)

Ha módosította a beépített szinkronizálási szabályokat, akkor ezek a szabályok a frissítéskor az alapértelmezett konfigurációra lesznek visszaállítva. Annak érdekében, hogy a konfiguráció a frissítések között is megmaradjon, győződjön meg arról, hogy az alapértelmezett konfiguráció módosításához ajánlott eljárásokban leírtak szerint [módosítja azokat.](how-to-connect-sync-best-practices-changing-default-configuration.md)

A helybeni frissítés során előfordulhatnak olyan módosítások, amelyek a frissítés befejezése után meghatározott szinkronizálási tevékenységeket igényelnek (beleértve a teljes importálás és a teljes szinkronizálási lépést). Az ilyen tevékenységek elhalasztásához olvassa el A [teljes szinkronizálás elhalasztása a frissítés után című szakaszt.](#how-to-defer-full-synchronization-after-upgrade)

Ha az Azure AD Connect nem szabványos összekötővel (például általános LDAP-összekötővel és általános SQL-összekötővel) használja, frissítenie kell a megfelelő összekötő konfigurációt a [szinkronizálási szolgáltatáskezelőben](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-connectors) a helybeni frissítés után. Az összekötő konfigurációjának frissítésével kapcsolatos részletekért tekintse meg az [Összekötő verziókiadási előzmények – Hibaelhárítás](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-connector-version-history#troubleshooting)című cikket. Ha nem frissíti a konfigurációt, az importálási és exportálási futtatási lépések nem fognak megfelelően működni az összekötőnél. A következő hibaüzenet jelenik meg az alkalmazás eseménynaplójában a *következő üzenettel: "Assembly verzió AAD-összekötő konfigurációban ("X.X.XXX. X") korábbi, mint a tényleges változat ("X.X.XXX. X") a "C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll" mappából.*

## <a name="swing-migration"></a>Párhuzamos migrálás
Ha összetett központi telepítéssel vagy sok objektumot rendelkezik, előfordulhat, hogy nem célszerű helyben frissíteni az élő rendszert. Egyes ügyfelek esetében ez a folyamat több napot is igénybe vehet , és ez idő alatt nem történik delta módosítás feldolgozása. Ezt a módszert akkor is használhatja, ha azt tervezi, hogy jelentős módosításokat hajt végre a konfiguráción, és ki szeretné próbálni őket, mielőtt a felhőbe lelöknék őket.

Az ajánlott módszer ezekhez a forgatókönyvekhez a swing-áttelepítés használata. Szüksége van (legalább) két kiszolgálóra – egy aktív kiszolgálóra és egy átmeneti kiszolgálóra. Az aktív kiszolgáló (az alábbi képen egyszínű kék vonalakkal) felelős az aktív termelési terhelésért. Az átmeneti kiszolgáló (szaggatott lila vonalakkal) az új kiadással vagy konfigurációval van előkészítve. Amikor teljesen készen áll, ez a kiszolgáló aktívvá vált. Az előző aktív kiszolgáló, amely most már a régi verzió vagy konfiguráció telepítve van, az átmeneti kiszolgálóra kerül, és frissül.

A két kiszolgáló különböző verziókat használhat. Például az aktív kiszolgáló, amely azt tervezi, hogy leszerelhető használhatja az Azure AD Sync, és az új átmeneti kiszolgáló használhatja az Azure AD Connect. Ha a swing áttelepítés segítségével új konfigurációt fejleszt ki, célszerű, hogy ugyanazokat a verziókat a két kiszolgálón.  
![Átmeneti kiszolgáló](./media/how-to-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Egyes ügyfelek inkább három vagy négy kiszolgálót szeretnenak ebben a forgatókönyvben. Az átmeneti kiszolgáló frissítésekénél nincs tartalék kiszolgáló a [vészhelyreállításhoz.](how-to-connect-sync-staging-server.md#disaster-recovery) Három vagy négy kiszolgáló esetén előkészítheti az elsődleges/készenléti kiszolgálók egy készletét az új verzióval, amely biztosítja, hogy mindig legyen egy átmeneti kiszolgáló, amely készen áll az átlépésre.

Ezek a lépések is dolgozik az Azure AD Sync vagy a FIM + Azure AD-összekötő megoldás áthelyezése. Ezek a lépések nem működnek a DirSync esetében, de ugyanaz a swing-áttelepítési módszer (más néven párhuzamos telepítés) a DirSync lépéseivel az [Azure Active Directory-szinkronizálás frissítése (DirSync)](how-to-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>A frissítéshez használjon lengőáttelepítést
1. Ha az Azure AD Connect mindkét kiszolgálón, és azt tervezi, hogy csak a konfiguráció módosítása, győződjön meg arról, hogy az aktív kiszolgáló és az átmeneti kiszolgáló egyaránt ugyanazt a verziót használja. Ez megkönnyíti a különbségek későbbi összehasonlítását. Ha az Azure AD Sync-ről frissít, akkor ezek a kiszolgálók különböző verziókkal rendelkeznek. Ha az Azure AD Connect egy régebbi verziójáról frissít, érdemes az azonos verziót használó két kiszolgálóval kezdeni, de ez nem kötelező.
2. Ha egyéni konfigurációt hozott létre, és az átmeneti kiszolgáló nem rendelkezik vele, kövesse az [Egyéni konfiguráció áthelyezése az aktív kiszolgálóról az átmeneti kiszolgálóra](#move-a-custom-configuration-from-the-active-server-to-the-staging-server)című csoport lépéseit.
3. Ha az Azure AD Connect korábbi kiadásáról frissít, frissítse az átmeneti kiszolgálót a legújabb verzióra. Ha az Azure AD Sync-ről költözik, telepítse az Azure AD Connectet az átmeneti kiszolgálóra.
4. Hagyja, hogy a szinkronizálási motor futtassa a teljes importálást és a teljes szinkronizálást az átmeneti kiszolgálón.
5. Ellenőrizze, hogy az új konfiguráció nem okozott-e váratlan módosításokat a [kiszolgáló konfigurációjának ellenőrzése](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server)című, "Ellenőrzés" című részében található lépésekkel. Ha valami nem a várt módon történik, javítsa ki, futtassa az importálást és a szinkronizálást, és ellenőrizze az adatokat, amíg jól nem néz ki a lépések végrehajtásával.
6. Váltson az átmeneti kiszolgálóra, hogy az legyen az aktív kiszolgáló. Ez az utolsó lépés "Váltás aktív szerver" a [konfiguráció ellenőrzése a szerver](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server).
7. Ha az Azure AD Connectet frissíti, frissítse a most átmeneti módban lévő kiszolgálót a legújabb kiadásra. Az adatok és a konfiguráció frissítéséhez kövesse ugyanazokat a lépéseket, mint korábban. Ha az Azure AD Sync-ről frissített, most kikapcsolhatja és leszerelheti a régi kiszolgálót.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Egyéni konfiguráció áthelyezése az aktív kiszolgálóról az átmeneti kiszolgálóra
Ha konfigurációs módosításokat hajtott végre az aktív kiszolgálón, meg kell győződnie arról, hogy ugyanazok a módosítások vonatkoznak az átmeneti kiszolgálóra. Az áthelyezés hez használhatja az [Azure AD Connect konfigurációs dokumentumot.](https://github.com/Microsoft/AADConnectConfigDocumenter)

Áthelyezheti az egyéni szinkronizálási szabályokat, amelyeket a PowerShell használatával hozott létre. Mindkét rendszeren ugyanúgy kell alkalmaznia a többi módosítást, és nem telepítheti át a módosításokat. A [konfigurációs dokumentumkezelő](https://github.com/Microsoft/AADConnectConfigDocumenter) segítségével összehasonlíthatja a két rendszert, hogy megbizonyosodjon arról, hogy azonosak. Az eszköz az ebben a szakaszban található lépések automatizálásában is segítséget nyújt.

Mindkét kiszolgálón a következő beállításokat kell ugyanúgy konfigurálnia:

* Csatlakozás ugyanahhoz az erdőhöz
* Bármely tartomány- és szervezetiegység-szűrés
* Ugyanazok a választható funkciók, például a jelszószinkronizálás és a jelszó-visszaírás

**Egyéni szinkronizálási szabályok áthelyezése**  
Az egyéni szinkronizálási szabályok áthelyezéséhez tegye a következőket:

1. Nyissa **meg a Szinkronizálási szabályok szerkesztőjét** az aktív kiszolgálón.
2. Jelöljön ki egy egyéni szabályt. Kattintson az **Export** (Exportálás) gombra. Ez egy jegyzettömb ablakot hoz létre. Mentse az ideiglenes fájlt PS1 kiterjesztéssel. Ez teszi a PowerShell-parancsfájl. Másolja a PS1 fájlt az átmeneti kiszolgálóra.  
   ![Szinkronizálási szabály exportálása](./media/how-to-upgrade-previous-version/exportrule.png)
3. Az összekötő GUID azonosítója eltér az átmeneti kiszolgálón, és módosítania kell azt. A GUID lekért elemével indítsa el a **Szinkronizálási szabályok szerkesztőjét,** válassza ki az ugyanazon csatlakoztatott rendszert képviselő beépített szabályok egyikét, majd kattintson az **Exportálás gombra.** Cserélje le a PS1-fájlguid azonosítóját az átmeneti kiszolgáló guid azonosítójára.
4. A PowerShell-parancssorban futtassa a PS1-fájlt. Ez létrehozza az egyéni szinkronizálási szabályt az átmeneti kiszolgálón.
5. Ismételje meg ezt az összes egyéni szabályesetében.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>A teljes szinkronizálás elhalasztása frissítés után
A helybeni frissítés során előfordulhatnak olyan módosítások, amelyek konkrét szinkronizálási tevékenységeket igényelnek (beleértve a Teljes importálás és a Teljes szinkronizálás lépést) a végrehajtáshoz. Például az összekötőséma módosításai **teljes importálási** lépést igényelnek, és a beépített szinkronizálási szabályok módosításai **teljes szinkronizálási** lépést igényelnek az érintett összekötőkön. A frissítés során az Azure AD Connect meghatározza, hogy milyen szinkronizálási tevékenységekre van szükség, és *felülírásként*rögzíti azokat. A következő szinkronizálási ciklusban a szinkronizálási ütemező felveszi ezeket a felülbírálásokat, és végrehajtja azokat. A felülbírálás sikeres végrehajtása után a rendszer eltávolítja azt.

Előfordulhatnak olyan helyzetek, amikor nem szeretné, hogy ezek a felülbírálások közvetlenül a frissítés után sor kerüljenek. Például számos szinkronizált objektumot tartalmaz, és azt szeretné, hogy ezek a szinkronizálási lépések munkaidő után történjenek. A felülbírálások eltávolítása:

1. A frissítés során törölje a **jelet** a **szinkronizálási folyamat indítása a konfiguráció befejezésekor jelölőnégyzetből.** Ez letiltja a szinkronizálási ütemezőt, és megakadályozza, hogy a szinkronizálási ciklus automatikusan megtörténjék a felülbírálások eltávolítása előtt.

   ![DisableFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync01.png)

2. A frissítés befejezése után futtassa a következő parancsmast, hogy megtudja, milyen felülírásokat adtak hozzá:`Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > A felülbírálások összekötő-specifikusak. A következő példában a Teljes importálás és a Teljes szinkronizálás i. lépés a helyszíni AD-összekötőhöz és az Azure AD-összekötőhöz is hozzá lett adva.

   ![DisableFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync02.png)

3. Jegyezze fel a meglévő felülírásokat, amelyeket hozzáadtak.
   
4. A teljes importálás és a teljes szinkronizálás felülbírálásának eltávolításához futtassa a következő parancsmast:`Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   A felülírások eltávolításához az összes összekötőn hajtsa végre a következő PowerShell-parancsfájlt:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Az ütemező folytatásához futtassa a következő parancsmast:`Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Ne felejtse el végrehajtani a szükséges szinkronizálási lépéseket a lehető leghamarabb. Ezeket a lépéseket manuálisan hajthatja végre a Szinkronizálási szolgáltatáskezelővel, vagy hozzáadhatja a felülbírálásokat a Set-ADSyncSchedulerConnectorOverride parancsmag használatával.

A teljes importálás és a teljes szinkronizálás felülbírálásának tetszőleges összekötőn történő hozzáadásához futtassa a következő parancsmast:`Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Hibaelhárítás
A következő szakasz hibaelhárítási és információkat tartalmaz, amelyek et használhat, ha problémát tapasztal az Azure AD Connect frissítése során.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Az Azure Active Directory-összekötő hiányzó hiba az Azure AD Connect frissítése során

Amikor az Azure AD Connectet egy korábbi verzióról frissíti, előfordulhat, hogy a frissítés elején bekövetkező hibaüzenet jelenik meg 

![Hiba](./media/how-to-upgrade-previous-version/error1.png)

Ez a hiba azért fordul elő, mert az Azure Active Directory-összekötő azonosítóval, b891884f-051e-4a83-95af-2544101c9083, nem létezik a jelenlegi Azure AD Connect konfiguráció. Ennek ellenőrzéséhez nyisson meg egy PowerShell-ablakot, futtassa a Parancsmag`Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083`

```
PS C:\> Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
Get-ADSyncConnector : Operation failed because the specified MA could not be found.
At line:1 char:1
+ Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ReadError: (Microsoft.Ident...ConnectorCmdlet:GetADSyncConnectorCmdlet) [Get-ADSyncConne
   ctor], ConnectorNotFoundException
    + FullyQualifiedErrorId : Operation failed because the specified MA could not be found.,Microsoft.IdentityManageme
   nt.PowerShell.Cmdlet.GetADSyncConnectorCmdlet

```

A PowerShell-parancsmag jelenti a **hibát, amelyet a megadott ma nem található.**

Ennek az az oka, hogy a jelenlegi Azure AD Connect-konfiguráció nem támogatott a frissítéshez. 

Ha az Azure AD Connect újabb verzióját szeretné telepíteni: zárja be az Azure AD Connect varázslót, távolítsa el a meglévő Azure AD Connectet, és végezze el az újabb Azure AD Connect tiszta telepítését.



## <a name="next-steps"></a>További lépések
További információ [a helyszíni identitások azure-active Directoryval való integrálásáról.](whatis-hybrid-identity.md)
