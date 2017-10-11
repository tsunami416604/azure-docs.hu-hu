---
title: "Az Azure AD Connect: Frissítés egy korábbi verzióról |} Microsoft Docs"
description: "Az Azure Active Directory Connect, beleértve a helyben frissítés és egy mozgó áttelepítési legújabb kiadására történő frissítése a különböző módszereket ismerteti."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 52fd9375c71c42feaf87f4a0f4220e1cb3889e63
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Az Azure AD Connect: Frissítés egy korábbi verziójáról a legújabb verzióra
Ez a témakör ismerteti a különböző módszereket, amelyek az Azure Active Directory (Azure AD) Connect telepítés frissítése a legújabb verzióra. Azt javasoljuk, hogy őrizze meg az Azure AD Connect kiadásainak aktuális. Is használhatja a lépéseket a [áttelepítési éppen](#swing-migration) szakaszában, amikor olyan jelentős konfigurációs módosítást hajt végre.

Ha azt szeretné, a frissítésre a Dirsyncről, lásd: [Azure AD Szinkronizáló eszközéről (DirSync) verzióról](active-directory-aadconnect-dirsync-upgrade-get-started.md) helyette.

Nincsenek használhatja az Azure AD Connect frissítése néhány különböző stratégiákat.

| Módszer | Leírás |
| --- | --- |
| [Automatikus frissítés](active-directory-aadconnect-feature-automatic-upgrade.md) |Ez az a legegyszerűbb módszer az expressz telepítési rendelkező ügyfelek esetén. |
| [Frissítés helyben](#in-place-upgrade) |Ha egy önálló kiszolgáló, frissítheti a telepítési helyben ugyanazon a kiszolgálón. |
| [Áttelepítési éppen](#swing-migration) |Két kiszolgáló előkészítése az új kiadási vagy konfigurációs kiszolgálón, és módosítsa az aktív kiszolgáló, amikor készen áll. |

Engedélyek információkért lásd: a [a frissítéshez szükséges engedélyek](active-directory-aadconnect-accounts-permissions.md#upgrade).

> [!NOTE]
> Az új Azure AD Connect kiszolgáló elindítani a változások az Azure AD szinkronizálása engedélyezését, meg kell állítja vissza a DirSync vagy az Azure AD Sync használatával. Az Azure AD Connect a régi ügyfelek, beleértve a DirSync és Azure AD Sync programot, alacsonyabb verziójúra változtatása nem támogatott, és az Azure ad-ben például adatvesztés vezethet.

## <a name="in-place-upgrade"></a>Frissítés helyben
Egy frissítés tér át Azure AD Sync vagy az Azure AD Connect működik. A Dirsync szolgáltatásról áthelyezésére vagy megoldás a Forefront Identity Manager (FIM) + Azure AD-összekötő nem működik.

Ez a módszer használata ajánlott, ha egy önálló kiszolgáló és kisebb, mint körülbelül 100 000 objektumok. Ha nincsenek out-of-box szinkronizálási szabályok módosításait, a teljes importálás és a teljes szinkronizálás történik a frissítés után. Ez a módszer biztosítja, hogy az új konfigurációt alkalmaz az összes meglévő objektumokat a rendszer. Ehhez a futtató órára is szükség lehet néhány, attól függően, hogy a szinkronizálási motor hatókörében lévő objektumok száma. A normál különbözeti szinkronizálás ütemező (amely alapértelmezés szerint 30 percenként szinkronizál) fel van függesztve, de a jelszó-szinkronizálás továbbra is. Érdemes lehet a helyi frissítés a hétvégén. Ha az az új Azure AD Connect a out-of-box konfigurációban nem történtek változások kiadási, majd a normál különbözeti importálás vagy szinkronizálás, ehelyett elindítja.  
![Frissítés helyben](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Ha a out-of-box szinkronizálási szabályok végrehajtott módosításokat, majd ezek a szabályok állítja vissza az alapértelmezett konfiguráció, a frissítés. Győződjön meg arról, hogy a frissítések közötti tárolódik a konfigurációt, győződjön meg arról, hogy módosítania folyamatban ismertetett módon [gyakorlati tanácsok az alapértelmezett konfiguráció módosításának](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

Helybeni verziófrissítés során előfordulhat bevezetett változások, a frissítés befejezése után hajtható végre a meghatározott szinkronizálási tevékenység (beleértve a teljes importálás és a teljes szinkronizálás lépést) szükséges. Az ilyen tevékenységek késleltető, tekintse át a részt [hogyan késlelteti a frissítés után teljes szinkronizálást](#how-to-defer-full-synchronization-after-upgrade).

## <a name="swing-migration"></a>Párhuzamos migrálás
Ha egy összetett üzembe helyezése vagy több objektumot, egy frissítés az élő rendszeren ehhez nem célszerű lehet. Egyes ügyfelek esetén ez a folyamat eltarthat több nap –, és ebben az időszakban nem változáskülönbözeteit feldolgozása. Is használhatja ezt a módszert, ha azt tervezi, hogy a konfiguráció jelentős változtatásokat, és próbálja ki őket ahhoz, azok még leküldeni a felhő szeretné.

Ezek a forgatókönyvek az ajánlott módszer, hogy egy mozgó áttelepítési használja. (Legalább) két kiszolgáló – egy aktív kiszolgáló és egy átmeneti kiszolgálón van szüksége. (Lásd az alábbi képen látható teli kék vonallal) az aktív kiszolgáló felelős a aktív üzemi terhelés. Az átmeneti kiszolgálón (látható lila szaggatott vonallal) az új kiadási vagy konfigurációs kész. Amikor teljesen készen áll, a kiszolgáló akkor aktív. A korábbi aktív kiszolgáló, amely mostantól a régi verziója vagy a konfigurációs telepítve van, az átmeneti kiszolgálóra történik, és frissítése.

A két kiszolgáló különböző verzióit is használhatják. Például az aktív kiszolgáló, amely le szeretné szerelni használhatja az Azure AD Sync, és az új átmeneti kiszolgálón használhatja az Azure AD Connect. Ha mozgó áttelepítés használatával fejlesztése a konfigurációt, célszerű egyeznie kell a két kiszolgálón.  
![Átmeneti kiszolgáló](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Egyes ügyfelek előnyben részesítik a három vagy négy kiszolgáló ehhez a forgatókönyvhöz. Az átmeneti kiszolgáló frissítve van, ha nem rendelkezik egy biztonsági kiszolgáló [vész-helyreállítási](active-directory-aadconnectsync-operations.md#disaster-recovery). Három vagy négy kiszolgálókkal készítse elő az új verzióra, amely biztosítja, hogy nincs-e mindig egy átmeneti kiszolgálón, amely kész átvenni az elsődleges vagy készenléti állapotban lévő kiszolgálók egy csoportja.

Ezeket a lépéseket az Azure AD Sync vagy az Azure AD-összekötő FIM kiegészítve megoldás mozgatása is működik. Ezeket a lépéseket a Dirsync nem működnek, de a azonos mozgó áttelepítés (más néven párhuzamos üzembe helyezés) lépésben DirSync módja a [frissítés Azure Active Directory-szinkronizálás (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>Egy mozgó áttelepítés használatával frissíti
1. Ha használja az Azure AD Connect mindkét kiszolgálón, és csak feltétlenül a konfiguráció módosítása, ellenőrizze, hogy az aktív kiszolgáló és az átmeneti kiszolgálón egyaránt ugyanazon verzióját használja. Amely megkönnyíti a különbségek később. Ha az Azure AD-Szinkronizálóról frissít, ezeket a kiszolgálókat különböző verziói működnek. Ha frissít, az Azure AD Connect egy korábbi verziójából származó, érdemes indítsa el a két kiszolgáló, amely ugyanazon verzióját használja, de ez nem szükséges.
2. Ha egy egyéni konfigurációt végrehajtott, és az átmeneti kiszolgálón nincs, kövesse a [az aktív kiszolgáló egyéni konfigurációs áthelyezése az átmeneti kiszolgálón](#move-custom-configuration-from-active-to-staging-server).
3. Ha az Azure AD Connect egy korábbi kiadásáról frissít, a átmeneti kiszolgáló frissítése a legújabb verzióra. Ha az Azure AD-Szinkronizálóról telepít át, majd az Azure AD Connect telepítése az átmeneti kiszolgálón.
4. Lehetővé teszik a szinkronizálási motor teljes importálást és teljes szinkronizálást a átmeneti kiszolgálón futnak.
5. Győződjön meg, hogy az új konfigurációt a "Hitelesítés" lépések segítségével nem okozhat váratlan módosításokat az [ellenőrizze a kiszolgáló konfigurációja](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Ha valami nem a várt módon, javítsa ki, futtassa az importálás és szinkronizálás, és ellenőrizze az adatokat, amíg azt megfelelőnek tűnik, a lépéseket követve.
6. Váltás az aktív kiszolgáló átmeneti kiszolgálón. Ez az utolsó lépésben "Kapcsoló aktív kiszolgáló" a [ellenőrizze a kiszolgáló konfigurációja](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Ha az Azure AD Connect frissít, frissítse a kiszolgálót, amelyik most már a legújabb verzióra átmeneti módban. Ugyanazokat a lépéseket, mielőtt az adatok és a frissített konfiguráció. Ha frissített az Azure AD-Szinkronizálóról, ezután kapcsolja ki a és a régi kiszolgáló leszerelése.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Az aktív kiszolgáló egyéni konfigurációs áthelyezése az átmeneti kiszolgálón
Ha az aktív kiszolgáló végrehajtott konfigurációs módosításokat, győződjön meg arról, hogy az azonos módosításai érvényesek lesznek a átmeneti kiszolgálón szeretné. Az áthelyezés érdekében használhatja a [az Azure AD Connect konfigurációs dokumentáló](https://github.com/Microsoft/AADConnectConfigDocumenter).

Áthelyezheti az egyéni szinkronizálási szabályokat, hogy a PowerShell használatával létrehozott. Telepítenie kell az egyéb módosítások ugyanúgy mindkét rendszeren, és nem telepíthető át a módosításokat. A [konfigurációs dokumentáló](https://github.com/Microsoft/AADConnectConfigDocumenter) segítségével összehasonlítja a két rendszer számára, győződjön meg arról, hogy azok egyezését. Az eszköz is segíthetnek az ebben a szakaszban található lépéseket automatizálását.

Mindkét kiszolgálón ugyanúgy konfigurálja a következőket kell:

* Az azonos erdők kapcsolat
* Bármely tartomány és szervezeti egységek szűrése
* A választható szolgáltatásait, például a jelszó-szinkronizálás és jelszóvisszaíró

**Helyezze át az egyéni szinkronizálási szabályok**  
Egyéni szinkronizálási szabályok áthelyezni, tegye a következőket:

1. Nyissa meg **szinkronizálási szabályok szerkesztő** az aktív kiszolgálón.
2. Jelöljön ki egy egyéni szabályt. Kattintson a **exportálása**. Ekkor megjelenik a Jegyzettömb ablak. Az ideiglenes fájl mentése PS1 kiterjesztéssel. Így egy PowerShell-parancsfájlt. Az átmeneti kiszolgálón másolja a PS1 fájlnevet.  
   ![Szinkronizálási szabály exportálása](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. Az összekötő GUID azonosítója nem egyezik a átmeneti kiszolgálón, és akkor kell megváltoztatnia. Ahhoz, hogy a GUID, indítsa el a **szinkronizálási szabályok szerkesztő**, válasszon egyet a out-of-box szabályokat, amelyek megfelelnek az adott csatlakoztatott rendszer, majd kattintson a **exportálása**. Az a PS1 fájlnevet a globálisan egyedi Azonosítót cserélje le a GUID-Azonosítójának a átmeneti kiszolgálón.
4. Egy PowerShell-parancssorba futtassa a PS1 fájlnevet. Ez az egyéni szinkronizálási szabályt a átmeneti kiszolgálón hoz létre.
5. Ismételje meg ezt az egyéni szabályok.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Hogyan késlelteti a frissítés után teljes szinkronizálást
Helybeni verziófrissítés során előfordulhat módosítások bevezetett (beleértve a teljes importálás és a teljes szinkronizálás lépést) adott szinkronizálási műveleteket hajthatnak végre. Például összekötő séma módosításához **teljes importálás** lépés és out-of-box szinkronizálási szabály módosításához **teljes szinkronizálás** lépéssel hajtható végre az érintett összekötőt. A frissítés során az Azure AD Connect meghatározza, hogy milyen szinkronizálási tevékenységek szükségesek, és rögzíti őket *felülbírálások*. A következő szinkronizálási ciklusban a szinkronizálás Feladatütemező szerzi be ezeket a felülbírálásokat, és végrehajtja azokat. Felülbírálás sikeresen végre, ha eltávolítják azt.

Előfordulhat, hogy esetekben nem célszerű ezeket a felülbírálásokat közvetlenül a frissítés után kerül sor. Például számos szinkronizált objektummal rendelkezik, és azt szeretné, hogy szinkronizálás lépések munkaidő után következik be. Ezeket a felülbírálásokat eltávolítása:

1. A frissítés során **, törölje a jelet** beállítás **a szinkronizálási folyamat indítása, ha a konfiguráció befejezése**. Ezzel letiltja a a szinkronizálási Feladatütemező, és megakadályozza, hogy a szinkronizálási ciklust megakadályozhat automatikusan előtt a rendszer eltávolítja a felülbírálások.

   ![DisableFullSyncAfterUpgrade](./media/active-directory-aadconnect-upgrade-previous-version/disablefullsync01.png)

2. Frissítés befejezése után futtassa az alábbi parancsmagot, hogy megtudja, milyen felülbírálások lettek hozzáadva:`Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > A felülbírálások connector-specifikus. A következő példában a teljes importálás és a teljes szinkronizálás lépést lettek hozzáadva mind a helyszíni AD-összekötő és az Azure AD-összekötőt.

   ![DisableFullSyncAfterUpgrade](./media/active-directory-aadconnect-upgrade-previous-version/disablefullsync02.png)

3. Jegyezze fel a meglévő felülbírálások hozzáadott.
   
4. A felülbírálásokat is teljes importálást és teljes szinkronizálást egy tetszőleges összekötőn eltávolításához futtassa a következő parancsmagot:`Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Távolítsa el az összes összekötőt a felülbírálások, hajtsa végre a következő PowerShell-parancsfájlt:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Az ütemező folytatásához futtassa az alábbi parancsmagot:`Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Ne felejtse el a szükséges szinkronizálásának lépései legkorábbi tetszés hajtható végre. Manuálisan hajtható végre ezeket a lépéseket a Synchronization Service Manager használatával, vagy adja hozzá a felülbírálások biztonsági másolatot, a Set-ADSyncSchedulerConnectorOverride parancsmaggal.

A teljes importálás és a teljes szinkronizálás felülbírálásokat egy tetszőleges összekötő hozzáadásához futtassa az alábbi parancsmagot:`Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="next-steps"></a>Következő lépések
További információ [a helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
