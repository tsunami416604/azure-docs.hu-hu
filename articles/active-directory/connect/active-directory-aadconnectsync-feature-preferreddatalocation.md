---
title: "Azure AD Connect szinkronizálása: elsődleges hely az Office 365-felhasználók konfigurálása |} Microsoft Docs"
description: "Ismerteti, hogyan kell állítania az Office 365 felhasználói erőforrások megközelíti a felhasználót az Azure AD Connect szinkronizálási szolgáltatás."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2018
ms.author: billmath
ms.openlocfilehash: 73b9b8d208b5eac2e62f62ab786efafa056e3cb4
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="azure-ad-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Azure AD Connect szinkronizálása: elsődleges hely az Office 365-erőforrások konfigurálása
Ha ez a témakör történései preferredDataLocation beállítása az Azure AD Connect szinkronizálási szolgáltatás célja. Ez az attribútum jelzi az Office 365, ahol a felhasználó, az erőforrások helyezhetik megközelíti a felhasználó szolgál. Ez a funkció a nagyobb ügyfelek számára készült.

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes megtekintéséhez és a felhőben alapértelmezés szerint kikapcsolva. Ha szeretné a részt venni az előzetes programban, lépjen kapcsolatba a Microsoft-képviselőjére.
>
>

## <a name="enable-synchronization-of-preferreddatalocation"></a>PreferredDataLocation szinkronizálásának engedélyezése
Alapértelmezés szerint a felhasználók az Office 365-erőforrások és az Azure AD-bérlő ugyanabban a régióban találhatók. Például ha a bérlő Észak-Amerika található, majd a felhasználó Exchange-postaládák is található Észak-Amerikában. Több nemzeti szervezet számára ez nem feltétlenül optimális. A felhasználó régió úgy, hogy az attribútum preferredDataLocation adható meg.

Beállítások alapján ez az attribútum, akkor a felhasználó Office 365 erőforrások, például a postaláda és a onedrive-ban ugyanabban a régióban, mint a felhasználónak pedig továbbra is a teljes szervezet számára több bérlő.

> [!IMPORTANT]
> Jogosult legyen ez a funkció az Office 365-előfizetéshez legalább 5000 munkaállomásokat kell rendelkeznie.
>
>

Az Office 365-ben a régiók a következők:

| Régió | Leírás |
| --- | --- |
| NÉV | Észak-Amerika |
| EUR | Európa |
| APC | Ázsia és a Csendes-óceáni térség |
| JPN | Japán |
| AUSZTRÁLIAI | Ausztrália |
| IS | Kanada |
| GBR | Nagy-Britannia |
| LAM | Latin-Amerika |

Nem minden Office 365 számítási feladattal támogatja a felhasználó régió beállítása.

Az Azure AD Connect szinkronizálásának támogatja a **PreferredDataLocation** az attribútum **felhasználói** objektumok verziójában 1.1.524.0 és után. Pontosabban a következő módosításokat vezettek:

* A séma az objektumtípus **felhasználói** a az Azure AD-összekötő az időtartam PreferredDataLocation attribútum, amely egyértékű karakterlánc típusú.

* A séma az objektumtípus **személy** a Metaverzumban ki van bővítve PreferredDataLocation attribútum, amely karakterlánc típusú, és egyértékű tartalmazza.

Alapértelmezés szerint a PreferredDataLocation attribútum nincs engedélyezve a szinkronizáláshoz. A szolgáltatás célja a nagyobb szervezeteknek, és nem mindenki számára előnyös azt. A felhasználók számára az Office 365 régió tárolására, mivel nincs a helyszíni Active Directoryban PreferredDataLocation attribútum attribútum is meg kell adnia. A topológiában a minden egyes szervezet különböző.

> [!IMPORTANT]
> Jelenleg az Azure AD lehetővé teszi, hogy a szinkronizált felhasználói objektumok és a felhő felhasználói PreferredDataLocation attribútum objektumok közvetlenül segítségével konfigurálható: az Azure AD PowerShell segítségével. Miután engedélyezte a szinkronizálási attribútum PreferredDataLocation, le kell állítania az Azure AD PowerShell segítségével állítsa be az attribútumot a **szinkronizált felhasználói objektumok** , az Azure AD Connect felülírják őket alapján a forrás attribútumértékek a helyszíni Active Directoryban.

> [!IMPORTANT]
> 2017. szeptember 1., mert az Azure AD már nem engedélyezi a PreferredDataLocation attribútum a **szinkronizált felhasználói objektumok** közvetlenül az Azure AD PowerShell kell konfigurálni. A szinkronizált felhasználói objektumok PreferredLocation attribútum konfigurálásához az Azure AD Connect kell használnia.

A szinkronizálás a PreferredDataLocation attribútum engedélyezése előtt kell:

 * Először döntse el, mely a helyszíni Active Directory-attribútumot, az adatforrás-attribútum használható. Típusúnak kell lennie **egyértékű karakterlánc**. Az egyik a extensionAttributes alábbi lépéseket a szolgál.

 * Ha korábban már konfigurálta a PreferredDataLocation attribútum a felhasználói objektumok meglévő szinkronizálva az Azure AD PowerShell Azure AD-ben, meg kell **backport** attribútumértékek a megfelelő felhasználói objektumok a helyszíni Active Directoryba.

    > [!IMPORTANT]
    > Ebben az esetben nem backport a helyszíni Active Directoryban a megfelelő felhasználói objektumok attribútum értékeket az Azure AD Connect eltávolítja a meglévő attribútumértékek az Azure AD, ha a szinkronizálás a PreferredDataLocation attribútum engedélyezve van.

 * Ajánlott a forrásattribútum konfigurálja a helyszíni legalább néhány AD-felhasználó objektumokat, ellenőrzésre később is használható.

A szinkronizálási attribútum PreferredDataLocation engedélyezésének lépései összegezhető:

1. Tiltsa le a szinkronizálásütemező, és ellenőrizze, hogy nincs folyamatban lévő szinkronizálás
2. Az adatforrás-attribútum hozzáadása a helyi ADDS összekötő séma
3. Az Azure AD-összekötő séma PreferredDataLocation hozzáadása
4. Az attribútum értékét a helyszíni Active Directoryból rendszer vonatkozó bejövő szinkronizálási szabály létrehozása
5. Az attribútumérték az Azure AD-rendszer kimenő szinkronizálási szabály létrehozása
6. Futtasson teljes szinkronizálási ciklust
7. Szinkronizálásütemezési engedélyezése
8. Az eredmény ellenőrzése

> [!NOTE]
> Ez a szakasz a többi ezeket a lépéseket, részleteket ismertet. Azok a környezet az Azure AD központi telepítés egyerdős topológiával rendelkező és anélküli egyéni szinkronizálási szabályait ismerteti. Ha egy Többerdős topológia, egyéni szinkronizálási szabályok konfigurálva, vagy egy átmeneti kiszolgálón rendelkezik, ennek megfelelően módosítsa a lépéseket kell.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1. lépés: Tiltsa le a szinkronizálásütemező, és ellenőrizze, hogy nincs folyamatban lévő szinkronizálás
Győződjön meg arról, szinkronizálás nem történik meg, amíg nem szándékolt módosításokat az Azure AD-exportálás alatt álló elkerülése érdekében a szinkronizálási szabályok frissítése közepén. A beépített szinkronizálásütemezési letiltása:

1. Indítsa el a PowerShell-munkamenetben az Azure AD Connect kiszolgálón.
2. A parancsmag futtatásával ütemezett szinkronizálás letiltása: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Indítsa el a **Synchronization Service Managert** címen **START** > **szinkronizálási szolgáltatás**.
4. Lépjen a **műveletek** lapra, és ellenőrizze, nincs az állapottal művelet *folyamatban*.

![Synchronization Service Managert - ellenőrizze, nincs folyamatban lévő műveletekkel](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step1.png)

### <a name="step-2-add-the-source-attribute-to-the-on-premises-adds-connector-schema"></a>2. lépés: Az adatforrás-attribútum hozzáadása a helyi ADDS összekötő séma
Nem minden AD attribútumot a rendszer importálta a helyszíni AD kapcsolódási térbe. Ha be van jelölve, alapértelmezés szerint nem szinkronizált attribútum használatára, akkor szüksége importálnia. Az adatforrás-attribútum hozzáadása az importált attribútumok listáját:

1. Lépjen a **összekötők** lapon a Synchronization Service Managert.
2. Kattintson a jobb gombbal a **helyszíni AD-összekötő** válassza **tulajdonságok**.
3. Az előugró párbeszédpanelen keresse meg a **attribútumok kiválasztása** fülre.
4. Ellenőrizze, hogy a kiválasztott használandó adatforrás-attribútum be van jelölve, a attribútum listában. Ha nem látja a attribútumot, majd kattintson a "Minden látszik" jelölőnégyzetet.
5. Kattintson a **OK** mentéséhez.

![Adatforrás-attribútum hozzáadása a helyszíni AD-összekötő séma](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>3. lépés: Az Azure AD-összekötő séma PreferredDataLocation hozzáadása
Alapértelmezés szerint a PreferredDataLocation attribútum nem importálja az Azure AD-kapcsolódási térbe kerülnek. Importált attribútumok listáját a PreferredDataLocation attribútum hozzáadása:

1. Lépjen a **összekötők** lapon a Synchronization Service Managert.
2. Kattintson a jobb gombbal a **Azure AD-összekötő** válassza **tulajdonságok**.
3. Az előugró párbeszédpanelen keresse meg a **attribútumok kiválasztása** fülre.
4. Attribútum listáján válassza ki a preferredDataLocation attribútum.
5. Kattintson a **OK** mentéséhez.

![Adatforrás-attribútum hozzáadása az Azure AD-összekötő séma](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step3.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>4. lépés: Az attribútum értékét a helyszíni Active Directoryból rendszer vonatkozó bejövő szinkronizálási szabály létrehozása
A bejövő szinkronizálási szabály lehetővé teszi a forrásattribútum a helyszíni Active Directoryból a Metaverzumba haladjanak attribútumérték:

1. Indítsa el a **szinkronizálási szabályok szerkesztő** címen **START** > **szinkronizálási szabályok szerkesztő**.
2. Állítsa be a keresési szűrő **irány** kell **bejövő**.
3. Kattintson a **új szabály hozzáadása** gombra kattintva hozzon létre egy új bejövő szabályt.
4. Az a **leírás** lapra, adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | --- | --- | --- |
    | Name (Név) | *Adjon meg egy nevet* | Például *"az ad-felhasználó PreferredDataLocation"* |
    | Leírás | *Adjon meg egy egyéni leírást* |  |
    | Csatlakoztatott rendszer | *Válassza ki a helyszíni AD-összekötő* |  |
    | Objektumtípus csatlakoztatva | **Felhasználó** |  |
    | Metaverzum-objektum típusa | **Person** |  |
    | Kapcsolat típusa | **Csatlakozás** |  |
    | Sorrend | *Válassza ki az 1 – 99 közötti szám* | 1 – 99 egyéni szinkronizálási szabályok számára van fenntartva. Nem válasszon egy másik szinkronizálási szabály által használt érték. |

5. Tartsa a **Scoping szűrő** üres összes objektumára. Szükség lehet az Azure AD Connect telepítési megfelelően tartalmazó szűrő végeznünk.
6. Lépjen a **átalakítása lapon** és valósítja meg a következő átalakítási szabályt:

    | Típusa | Célattribútum | Forrás | Egyszer alkalmazása | Egyesítési típus |
    | --- | --- | --- | --- | --- |
    |Közvetlen | PreferredDataLocation | Válassza ki az adatforrás-attribútum | Nincs bejelölve | Frissítés |

7. Kattintson a **Hozzáadás** a bejövő szabály létrehozásához.

![Bejövő szinkronizálási szabályának létrehozása](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>5. lépés: Az attribútum értéke az Azure AD-rendszer kimenő szinkronizálási szabály létrehozása
A kimenő szinkronizálási szabály lehetővé teszi az attribútumérték felé haladjanak a Metaverzumba a PreferredDataLocation attribútumhoz az Azure ad-ben:

1. Lépjen a **szinkronizálási szabályok** szerkesztő.
2. Állítsa be a keresési szűrő **irány** kell **kimenő**.
3. Kattintson a **új szabály hozzáadása** gombra.
4. Az a **leírás** lapra, adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | ----- | ------ | --- |
    | Name (Név) | *Adjon meg egy nevet* | Ha például a "Kimenő az aad-ben – felhasználói PreferredDataLocation" |
    | Leírás | *Adjon meg egy leírást* ||
    | Csatlakoztatott rendszer | *Válassza ki az AAD-összekötő* ||
    | Objektumtípus csatlakoztatva | Felhasználó ||
    | Metaverzum-objektum típusa | **Person** ||
    | Kapcsolat típusa | **Csatlakozás** ||
    | Sorrend | *Válassza ki az 1 – 99 közötti szám* | 1 – 99 egyéni szinkronizálási szabályok számára van fenntartva. Nem válasszon egy másik szinkronizálási szabály által használt érték. |

5. Lépjen a **Scoping szűrő** lapra, és adja hozzá a **egyetlen tartalmazó szűrő csoportban található, két záradékok**:

    | Attribútum | Operátor | Érték |
    | --- | --- | --- |
    | sourceObjectType | EGYENLŐ | Felhasználó |
    | cloudMastered | NOTEQUAL | True (Igaz) |

    Hatókörként szűrő határozza meg, mely az Azure AD-objektumokat a kimenő szinkronizálási szabály vonatkozik. A jelen példában használjuk "Az AD-felhasználók identitását Out" azonos tartalmazó szűrőjének OOB-szinkronizálási szabály. Megakadályozza, hogy a szinkronizálási szabály alkalmazták felhasználói objektumok, amelyek nincsenek szinkronizálva a helyszíni Active Directoryból. Szükség lehet az Azure AD Connect telepítési megfelelően tartalmazó szűrő végeznünk.

6. Lépjen a **átalakítása** lapra, és valósítja meg a következő átalakítási szabályt:

    | Típusa | Célattribútum | Forrás | Egyszer alkalmazása | Egyesítési típus |
    | --- | --- | --- | --- | --- |
    | Közvetlen | PreferredDataLocation | PreferredDataLocation | Nincs bejelölve | Frissítés |

7. Bezárás **Hozzáadás** a kimenő forgalomra vonatkozó szabály létrehozásához.

![Kimenő szinkronizálási szabály létrehozása](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>6. lépés: Teljes szinkronizálás futtatása ciklus
Általában teljes szinkronizálási ciklust azért szükséges, mert mindkét az ad jelentek meg új attribútumokat és Azure AD-összekötőt, valamint a bevezetett egyéni szinkronizálási szabályait. Javasoljuk, hogy a módosítások ellenőrzéséhez őket az Azure AD-exportálás előtt. Az alábbi lépések segítségével a módosítások ellenőrzéséhez a lépéseket egy teljes szinkronizálási ciklust alkotó manuális futtatása során.

1. Futtatás **teljes importálás** a lépés a **helyszíni AD-összekötő**:

   1. Lépjen a **műveletek** lapon a Synchronization Service Managert.
   2. Kattintson a jobb gombbal a **helyszíni AD-összekötő** válassza **futtatása...** .
   3. Az előugró párbeszédpanelen válassza ki a **teljes importálás** kattintson **OK**.
   4. Várjon, amíg a művelet befejezését.

    > [!NOTE]
    > Teljes importálás kihagyhatja a helyszíni AD-összekötő, ha az adatforrás-attribútum már szerepel a listájában importált attribútumok. Ez azt jelenti, hogy nem rendelkezett során bármilyen módosítást [2. lépés: az adatforrás-attribútum hozzáadása a helyszíni AD-összekötő séma](#step-2-add-the-source-attribute-to-the-on-premises-adds-connector-schema).

2. Futtatás **teljes importálás** a lépés a **Azure AD-összekötő**:

   1. Kattintson a jobb gombbal a **Azure AD-összekötő** válassza **futtatása...**
   2. Az előugró párbeszédpanelen válassza ki a **teljes importálás** kattintson **OK**.
   3. Várjon, amíg a művelet befejezését.

3. A felhasználói objektum, a szinkronizálási szabály módosítások ellenőrzéséhez:

A forrásattribútumot, a helyszíni Active Directory és az Azure AD PreferredDataLocation importálta a megfelelő kapcsolódási térbe kerülnek. A teljes szinkronizálás lépés folytatása előtt javasoljuk, hogy végezzen egy **előzetes** egy meglévő felhasználó az objektum a helyszíni AD kapcsolódási térbe. Az objektum, amelyet kivételezett kell rendelkeznie a forrásattribútum feltöltve. A sikeres **előzetes** a feltöltve a Metaverzumban PreferredDataLocation egy jól jelzi, hogy konfigurálta a szinkronizálás szabályainak megfelelően. Ehhez információ egy **előzetes**, tekintse át a részt [ellenőrzéséhez](active-directory-aadconnectsync-change-the-configuration.md#verify-the-change).

4. Futtatás **teljes szinkronizálás** a lépés a **helyszíni AD-összekötő**:

   1. Kattintson a jobb gombbal a **helyszíni AD-összekötő** válassza **futtatása...** .
   2. Az előugró párbeszédpanelen válassza ki a **teljes szinkronizálás** kattintson **OK**.
   3. Várjon, amíg a művelet befejezését.

5. Győződjön meg arról **függőben lévő Exportálásokról** az Azure AD:

   1. Kattintson a jobb gombbal a **Azure AD-összekötő** válassza **Összekötőtér keresési**.
   2. Összekötőtér keresési előugró párbeszédpanelen:

      1. Állítsa be **hatókör** való **exportálási függőben lévő**.
      2. Ellenőrizze a három jelölőnégyzetet, beleértve a **hozzáadása, módosítása és törlése**.
      3. Kattintson a **keresési** gombra kell kattintania a módosítások exportálható objektumok listáját. Vizsgálja meg az adott objektum a módosításokat, kattintson duplán az objektum.
      4. Ellenőrizze, hogy a változások várhatók.

6. Futtatás **exportálása** a lépés a **Azure AD Connectoron**

   1. Kattintson a jobb gombbal a **Azure AD-összekötő** válassza **futtatása...** .
   2. Az összekötő futtatásához előugró párbeszédpanelen válassza ki **exportálása** kattintson **OK**.
   3. Várjon, amíg befejeződik az Azure Active Directoryba való exportálás.

> [!NOTE]
> Előfordulhat, hogy a lépéseket tartalmazza a teljes szinkronizálás lépésben az Azure AD Connectoron, és az AD Connectoron exportálás. A lépések esetén nincs szükség, mert az attribútum értékei vannak áramlanak a helyszíni Active Directory csak az Azure ad.

## <a name="step-7-re-enable-sync-scheduler"></a>7. lépés: Engedélyezze újra a szinkronizálásütemező
Engedélyezze újra a beépített szinkronizálásütemezési:

1. Indítsa el a PowerShell-munkamenetben.
2. Újra engedélyezi az ütemezett szinkronizálás parancsmag futtatásával:`Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>8. lépés: Az eredmény ellenőrzése
Ellenőrizze a konfigurációt, majd engedélyezze a felhasználók számára az idő már.

1. A régió adja hozzá a felhasználó a kijelölt attribútumot. Az elérhető régiók listáját található [ezt a táblázatot](#enable-synchronization-of-preferreddatalocation).  
![A felhasználó hozzá AD attribútum](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Várjon, amíg az attribútum az Azure AD-szinkronizálásának engedélyezése.
3. Exchange Online PowerShell használ, győződjön meg arról, hogy a postaláda régió helyesen van beállítva.  
![Postaláda-régiót állítsa be a felhasználó az Exchange Online](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Feltéve, hogy a bérlő jelölte meg kell tudni használni a szolgáltatás, a postaláda áthelyezése a megfelelő régióba. Ez a kiszolgálónév megnézi, ahol a postaláda ellenőrizheti.
4. Győződjön meg arról, hogy ez a beállítás volt hatékony sok postafiókot, használja a parancsfájl a [Technet gallery](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e). Ezt a parancsfájlt is rendelkezik Office 365 adatközpontok server előtagok és amely listája régióban található. Használat az előző lépésben hivatkozásként való ellenőrizze a postaláda helyét.

## <a name="next-steps"></a>További lépések
* További információk a konfigurációs modell [ismertetése deklaratív kiépítés](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* További információk az a kifejezés nyelv [ismertetése deklaratív kiépítés kifejezések](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Áttekintő témakör**

* [Azure AD Connect szinkronizálása: megértéséhez, valamint a szinkronizálás testreszabása](active-directory-aadconnectsync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
