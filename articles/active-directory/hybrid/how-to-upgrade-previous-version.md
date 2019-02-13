---
title: 'Azure AD Connect: Frissítés korábbi verzióról |} A Microsoft Docs'
description: Ismerteti a különböző módszerek frissítése a legújabb kiadása az Azure Active Directory Connect, beleértve a helyben frissítés és a egy párhuzamos migrálás.
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
ms.date: 07/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 437577ec68ee825bd0815735fef08e8297dad756
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180539"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: Egy előző verzióról a legújabbra frissítése
Ez a témakör ismerteti a különböző módszereket, amelyek segítségével az Azure Active Directory (Azure AD) Connect-telepítésre frissítsen a legújabb verziót. Azt javasoljuk, hogy őrizze meg magát a kiadások, az Azure AD Connect az aktuális. Is szereplő lépések segítségével a [párhuzamos migrálás](#swing-migration) szakaszt, ha létrehoz egy jelentős konfigurációs módosítást.

Ha azt szeretné, hogy frissítésre a dirsyncről, [frissítése az Azure AD Szinkronizáló eszközéről (DirSync)](how-to-dirsync-upgrade-get-started.md) helyette.

Van néhány különböző stratégiákat használhatja az Azure AD Connect frissítése.

| Módszer | Leírás |
| --- | --- |
| [Automatikus frissítés](how-to-connect-install-automatic-upgrade.md) |Ez az a legegyszerűbb módszer az expressz telepítési rendelkező ügyfelek számára. |
| [Frissítés helyben](#in-place-upgrade) |Ha rendelkezik egy egykiszolgálós, frissítheti a telepítési helyben ugyanazon a kiszolgálón. |
| [Párhuzamos migrálás](#swing-migration) |Két kiszolgáló előkészítése az új kiadás vagy konfigurációs kiszolgálók egyikét, és módosítsa az aktív kiszolgáló, amikor készen áll. |

Engedélyek információkért lásd: a [a frissítéshez szükséges engedélyek](reference-connect-accounts-permissions.md#upgrade).

> [!NOTE]
> Miután engedélyezte az új Azure AD Connect-kiszolgáló, kezdje a változásokat az Azure ad-hez, nem térhet vissza a DirSync vagy Azure AD Sync. Az Azure AD Connect a régebbi ügyfelekre, beleértve a DirSync és az Azure AD Sync és alacsonyabb verziójúra változtatása nem támogatott, és az Azure ad-ben problémákat, például adatvesztést okozhat.

## <a name="in-place-upgrade"></a>Frissítés helyben
Egy helyszíni frissítését az Azure AD Sync vagy az Azure AD Connect való áttérés működik. A Dirsync szolgáltatásról történő áthelyezésére vagy a megoldás a Forefront Identity Manager (FIM) + Azure AD-összekötő nem működik.

Ez a módszer használata ajánlott, ha egyetlen kiszolgálón és a kisebb, mint körülbelül 100 000 objektumra. Ha módosítania kellene az out-of-box szinkronizálási szabályokat, a teljes importálást és teljes szinkronizálást a frissítés után történik. Ez a módszer biztosítja, hogy az új konfiguráció alkalmazása az összes meglévő objektumokat a rendszer. Futtatni, terjed ki a szinkronizálási motor objektumok számától függően néhány órát is igénybe vehet. A normál különbözeti szinkronizálás ütemező (amely alapértelmezés szerint szinkronizálja a 30 percenként) fel van függesztve, de a jelszó-szinkronizálás továbbra is. Célszerű a helyszíni frissítését egy hétvégén. Ha kiadás az out-of-box-konfigurációt az új Azure AD Connect nem változik, majd egy normál különbözeti importálás/szinkronizálás, inkább elindul.  
![Frissítés helyben](./media/how-to-upgrade-previous-version/inplaceupgrade.png)

Ha az out-of-box szinkronizálási szabályokon végrehajtott módosításokat, majd ezek a szabályok állítja vissza az alapértelmezett konfiguráció, a frissítés. Győződjön meg arról, hogy a konfigurációs frissítések között tárolódik, győződjön meg arról, hogy módosításokat, hogy már leírtak szerint [ajánlott eljárások az alapértelmezett konfiguráció módosításának](how-to-connect-sync-best-practices-changing-default-configuration.md).

A helyben frissítés során, előfordulhat, bevezetett változások igénylő frissítés befejezése után futtatandó meghatározott szinkronizálási tevékenység (beleértve a teljes importálást és teljes szinkronizálást lépést). Ilyen tevékenységek késlelteti, tekintse meg a szakasz [késlelteti a frissítés után teljes szinkronizálást hogyan](#how-to-defer-full-synchronization-after-upgrade).

Ha az Azure AD Connect nem szabványos összekötővel (például az általános LDAP-összekötő és az általános SQL-összekötő) használ, frissítenie kell a megfelelő, az összekötő-konfiguráció a [Synchronization Service Managert](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-connectors) helybeni verziófrissítés. Az összekötő konfigurációjának frissítése a részletekért tekintse meg a cikk a szakasz [Connector Verziókiadásai – hibaelhárítás](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-connector-version-history#troubleshooting). Ha nem frissíti a konfigurációt, a behozatali és kiviteli lépéseket nem működnek megfelelően az összekötőhöz. Az alkalmazások eseménynaplójában, az üzenet a következő hibaüzenetet kap *"szerelvény verziója az AAD-összekötő konfigurációja ("X.X.XXX. "X") már korábban, mint a tényleges verzió ("X.X.XXX. "X") a "C:\Program Files\Microsoft Azure ad-ben Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll".*

## <a name="swing-migration"></a>Párhuzamos migrálás
Ha egy összetett telepítést vagy több objektum van, az élő rendszer helyben történő frissítés nem célszerű lehet. Egyes ügyfelek számára a folyamat több napot--is igénybe vehet, és ebben az időszakban nem változásokat feldolgozása. Is használhatja ezt a módszert, ha azt tervezi, hogy a konfiguráció jelentős változtatásokat, és próbálja ki őket, mielőtt azok már leküldte a felhő szeretne.

Az ajánlott módszer az ilyen feladatokhoz szükséges, hogy a párhuzamos migrálás. (Legalább) két kiszolgáló – több aktív kiszolgálót és a egy átmeneti kiszolgálón van szüksége. (A folytonos kék vonal az alábbi ábrán látható) az aktív kiszolgáló felelős az aktív éles terhelés. Az átmeneti kiszolgálón (a szaggatott lila vonal látható) az új kiadás vagy a konfiguráció kész. Teljes mértékben készen áll, amikor a kiszolgáló akkor aktív. Az előző aktív kiszolgáló, amely most már van a régi verziót vagy a telepített konfigurációs, az átmeneti kiszolgálóra történik, és frissül.

A két kiszolgáló is eltérő verziókat használnak. Például az aktív kiszolgáló, amely le szeretné szerelni használhatja az Azure AD Sync szolgáltatásról, és az új átmeneti kiszolgáló az Azure AD Connect használatával. Párhuzamos migrálás használatával egy új konfiguráció fejlesztése, célszerű a egyeznie kell a két kiszolgáló található.  
![Átmeneti kiszolgáló](./media/how-to-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Egyes ügyfelek inkább három vagy négy kiszolgálót ebben a forgatókönyvben. Ha az átmeneti kiszolgáló frissítve van, nem szükséges a biztonsági mentési kiszolgálóra [vész-helyreállítási](how-to-connect-sync-operations.md#disaster-recovery). Három vagy négy kiszolgálókkal készítse elő az új verziót, amely biztosítja, hogy mindig van egy átmeneti kiszolgálón, amely készen áll a átveszi az elsődleges vagy készenléti állapotban lévő kiszolgálók egy csoportja.

Ezeket a lépéseket áthelyezése az Azure AD Sync vagy az FIM + az Azure AD-összekötő megoldással is működik. Ezeket a lépéseket nem működnek a Dirsync szinkronizálással, de a azonos párhuzamos áttelepítés (más néven párhuzamos üzembe helyezés) a lépéseket a DirSync módja a [frissítése az Azure Active Directory sync (DirSync)](how-to-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>A párhuzamos áttelepítés használatával frissíti
1. Ha az Azure AD Connect mindkét kiszolgálón, és tervezze tétele csak a konfiguráció módosítása, ellenőrizze, hogy az aktív kiszolgáló és az átmeneti kiszolgálón egyaránt használja ugyanazt a verziót. Amely megkönnyíti a különbségek később. Ha frissít, az Azure AD-Szinkronizálóról, akkor ezeket a kiszolgálókat különböző verziói rendelkezik. Ha az Azure AD Connect egy régebbi verziójáról frissít, célszerű elindítani a két kiszolgáló, amely ugyanazt a verziót használ, de ez nem szükséges.
2. Ha az átmeneti kiszolgáló nem rendelkezik, egy egyéni konfigurációt végrehajtott, kövesse a [az aktív kiszolgáló egyéni konfigurációs áthelyezése az átmeneti kiszolgáló](#move-a-custom-configuration-from-the-active-server-to-the-staging-server).
3. Ha frissít, az Azure AD Connect egy korábbi verziója, az átmeneti kiszolgáló frissítése a legújabb verzióra. Ha az Azure AD-Szinkronizálóról telepít át, majd az Azure AD Connect telepítése az átmeneti kiszolgálón.
4. A szinkronizálási motor teljes importálást és teljes szinkronizálást futtatni az átmeneti kiszolgálón legyen.
5. Ellenőrizze, hogy az új konfigurációt a lépésekkel, a "Hitelesítés" nem okozhat váratlan módosítások be [ellenőrizze a konfigurációs kiszolgáló](how-to-connect-sync-operations.md#verify-the-configuration-of-a-server). Ha valami nem a várt módon, javítsa ki, és futtassa az importálás és szinkronizálás, és ellenőrizze az adatokat, amíg nem, minden rendben, az alábbi lépéseket.
6. Váltás az átmeneti kiszolgálót az aktív kiszolgáló. Ez az utolsó lépésben "Kapcsoló active server" [ellenőrizze a konfigurációs kiszolgáló](how-to-connect-sync-operations.md#verify-the-configuration-of-a-server).
7. Ha frissít, az Azure AD Connect, frissítse a kiszolgálót, amelyet már az átmeneti környezetű üzemmód legfrissebb verzióját. Ugyanezekkel a lépésekkel, mielőtt az adatokat és a frissített konfiguráció beolvasásához. Ha frissített az Azure AD-Szinkronizálóról, most kapcsolja ki és a régi kiszolgáló leszerelése.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Az aktív kiszolgáló egyéni konfigurációs áthelyezése az átmeneti kiszolgálón
Ha az aktív kiszolgáló végrehajtott konfigurációs módosítások, győződjön meg arról, hogy a módosításokat az átmeneti kiszolgálón vannak alkalmazva szeretné. Az áthelyezési érdekében használhatja a [az Azure AD Connect konfigurációs dokumentáló](https://github.com/Microsoft/AADConnectConfigDocumenter).

Továbbléphet az egyéni szinkronizálási szabályokat, hogy létrehozott PowerShell-lel. Telepítenie kell más módosítást ugyanúgy mindkét rendszereken, és nem telepíthető át a módosításokat. A [konfigurációs dokumentáló](https://github.com/Microsoft/AADConnectConfigDocumenter) összehasonlítása, győződjön meg arról, hogy megegyeznek a két rendszer segítségével. Az eszköz is segít a jelen szakaszban található lépéseket automatizálását.

Mindkét kiszolgálón ugyanúgy konfigurálhatja a következőkre van szüksége:

* Kapcsolat az azonos erdőkhöz
* Bármely tartomány és szervezeti egységek szűrése
* Az azonos választható funkciók, például a jelszó-szinkronizálás és jelszóvisszaíró

**Egyéni szinkronizálási szabályok áthelyezése**  
Egyéni szinkronizálási szabályok áthelyezni, tegye a következőket:

1. Nyissa meg **szinkronizálási Szabályszerkesztővel** az aktív kiszolgáló.
2. Jelöljön ki egy egyéni szabályt. Kattintson a **exportálása**. Ez megnyit egy Jegyzettömb-ablakot. Az ideiglenes fájlt mentse PS1 kiterjesztéssel. Így egy PowerShell-parancsfájlt. Az átmeneti kiszolgálón másolja a PS1 fájlnevet.  
   ![Szinkronizálási szabály exportálása](./media/how-to-upgrade-previous-version/exportrule.png)
3. Az összekötő GUID-azonosítója nem egyezik az átmeneti kiszolgálón, és módosítania kell. A globálisan egyedi Azonosítót kap, először **szinkronizálási Szabályszerkesztővel**, válassza ki a beépített szabályokat, amelyek azonos csatlakoztatott rendszer képviselik, és kattintson az egyik **exportálása**. A globálisan egyedi Azonosítót a PS1 fájlban cserélje le az átmeneti kiszolgáló származó GUID Azonosítóval.
4. Egy PowerShell-parancssorban futtassa a PS1 fájlnevet. Ez létrehozza a egyéni szinkronizálási szabályt az átmeneti kiszolgálón.
5. Ismételje meg ezt az egyéni szabályokat.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Hogyan késlelteti a frissítés után teljes szinkronizálást
A helyben frissítés során, előfordulhat, bevezetett változások igénylő futtatandó meghatározott szinkronizálási tevékenység (beleértve a teljes importálást és teljes szinkronizálást lépést). Például összekötő sémaváltozások igényelnek **teljes importálást** lépés és out-of-box szinkronizálási szabály megváltoztatása igényli **teljes szinkronizálás** lépéssel hajtható végre, az érintett összekötőt. A frissítés során az Azure AD Connect határozza meg, milyen szinkronizálási tevékenység szükség, és rögzíti őket *felülbírálások*. A következő szinkronizálási ciklusban a szinkronizálásütemező ezeket a felülbírálásokat és egy naplóbejegyzésbe hajtja végre őket. Miután sikeresen hajtja végre a felülbírálást, a rendszer eltávolítja.

Előfordulhat, hogy amennyiben nem szeretné ezeket a felülbírálásokat, hogy végre lehessen hajtani a frissítés után azonnal helyzetekben. Például számos szinkronizált objektummal rendelkezik, és szeretné ezeket a szinkronizálási lépéseket munkaidő után fordul elő. Ezeket a felülbírálásokat eltávolítása:

1. A frissítés során **törölje a jelet** lehetőséget **indítsa el a szinkronizálást, amint a konfigurálás befejeződik**. Ez a szinkronizálásütemező letiltja, és megakadályozza, hogy a szinkronizálási ciklust zajlik automatikusan előtt a felülbírálások el lesznek távolítva.

   ![DisableFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync01.png)

2. Frissítés befejezése után futtassa a következő parancsmag ismerje meg, mely a felülbírálások lettek hozzáadva: `Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > A felülbírálások összekötő-specifikus. A következő példában a teljes importálást és teljes szinkronizálást lépést lettek hozzáadva a mind a helyszíni AD-összekötő és az Azure AD-összekötőt.

   ![DisableFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync02.png)

3. Jegyezze fel a meglévő felülbírálások hozzáadott.
   
4. A felülbírálásokat is teljes importálást és teljes szinkronizálást egy tetszőleges összekötő eltávolításához futtassa a következő parancsmagot: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   A felülbírálások, az összes összekötő eltávolításához hajtsa végre a következő PowerShell-parancsfájlt:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Az ütemező újraindításához futtassa a következő parancsmagot: `Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Ne feledje, a szinkronizálás szükséges lépéseket az Önnek legkorábbi végrehajtásához. Manuálisan hajtható végre ezeket a lépéseket a Synchronization Service Managert használatával, vagy adja hozzá a felülbírálások biztonsági másolatot, a Set-ADSyncSchedulerConnectorOverride parancsmaggal.

Egy tetszőleges összekötő teljes importálást és teljes szinkronizálást a felülbírálás hozzáadásához futtassa az alábbi parancsmagot:  `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Hibaelhárítás
A következő szakasz tartalmazza a hibaelhárítás és információt, amelyet használhat, ha az Azure AD Connect frissítése problémát tapasztal.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Az Azure Active Directory connector hiányzó hiba során az Azure AD Connect frissítése

Amikor az Azure AD Connect egy korábbi verziójáról frissít, előfordulhat, hogy eléri a következő hiba történt a frissítés kezdetén 

![Hiba](./media/how-to-upgrade-previous-version/error1.png)

Ez a hiba akkor fordul elő, mert azonosítójú b891884f-051e-4a83-95af - 2544101c 9083, az Azure Active Directory-összekötő nem létezik az aktuális Azure AD Connect konfiguráció. Ellenőrizze, hogy ez a helyzet, nyissa meg egy PowerShell-ablakot, a parancsmag futtatása `Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083`

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

A PowerShell-parancsmag jelzi a hiba **nem található a megadott MA**.

Ennek oka, mert az aktuális Azure AD Connect konfiguráció frissítése nem támogatott. 

Ha szeretné-e az Azure AD Connect egy újabb verzió telepítése: az Azure AD Connect varázsló bezárásához, távolítsa el a meglévő Azure AD Connect és az újabb Azure AD Connect tiszta telepítését.



## <a name="next-steps"></a>További lépések
Tudjon meg többet [a helyszíni identitások integrálása az Azure Active Directory](whatis-hybrid-identity.md).
