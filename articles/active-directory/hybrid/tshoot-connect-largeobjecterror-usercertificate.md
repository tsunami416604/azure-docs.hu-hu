---
title: Az Azure AD Connect - userCertificate attribútum által okozott LargeObject hibák |} A Microsoft Docs
description: Ez a témakör a javítási lépések userCertificate attribútum által okozott LargeObject hibák.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: c851b5ef024e6584e6f8c93995208b08a91fbb60
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58096673"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Az Azure AD Connect szinkronizálása: UserCertificate attribútum által okozott LargeObject hibák kezelése

Az Azure AD kikényszeríti a maximálisan megengedett **15** értékek tanúsítvány a **userCertificate** attribútum. Az Azure AD Connect több mint 15 értéket tartalmazó objektumot exportálja az Azure ad-ben, ha az Azure AD adja vissza egy **LargeObject** történt a következő üzenettel:

>*"A telepített objektum mérete túl nagy. Csökkentse az attribútum objektum értékeinek számát. A művelet... a következő szinkronizálási ciklusban megpróbálja ismét"*

A LargeObject hiba okozhatja más AD-attribútumok. Győződjön meg róla, valóban a userCertificate attribútum által okozott, ellenőrizze az objektum vagy a helyszíni AD- vagy a kell a [Synchronization Service Managert keresés a Metaverzumban](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-mvsearch).

Az objektumok listájában, a bérlőben LargeObject-hibák beszerzéséhez használja a következő módszerek egyikét:

 * Ha a bérlő Azure AD Connect Health szinkronizálási szolgáltatás engedélyezve van, olvassa el a [szinkronizálási hibajelentés](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-sync) megadott.
 
 * Az egyes szinkronizálási ciklus végén küldött értesítő e-mail a címtár-szinkronizálási hibák az objektumok LargeObject-hibák listája szerepel. 
 * A [szinkronizálási szolgáltatáskezelő műveleti lapon](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-operations) kattintva legújabb Exportálás az Azure ad-ben a művelet a LargeObject-hibák objektumok listáját jeleníti meg.
 
## <a name="mitigation-options"></a>Megoldás beállításai
A LargeObject hiba megszüntetéséig egyéb attribútumainak módosítása ugyanazon az objektumon nem lehet exportálni az Azure ad-hez. A hiba elhárításához fontolja meg a következő beállításokat:

 * Azure AD Connect 1.1.524.0 hozhat létre frissítése vagy után. Az Azure AD Connect 1.1.524.0, az out-of-box szinkronizálási szabályok frissítve lett-e, nem exportálhatja a attribútumok userCertificate és a userSMIMECertificate, ha az attribútumok több mint 15 értéket kell létrehozni. Az Azure AD Connect frissítése a részletekért tekintse meg a cikk [az Azure AD Connect: Frissítés egy előző verzióról a legújabbra](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

 * Alkalmazzon egy **kimenő szinkronizálási szabály** az Azure AD Connectben exportálhatók egy **null érték helyett a tényleges értékek objektumok 15-nél több tanúsítvány értékekkel**. Ez a beállítás akkor megfelelő, ha nincs szüksége a tanúsítvány értékeket nelze exportovat do Azure AD-objektum több mint 15 értéket. A szinkronizálási szabály megvalósítása a részletekért tekintse meg a következő szakaszban [Implementing szinkronizálási szabályt, amely korlátozza az exportálási userCertificate attribútum](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * A helyszíni tanúsítvány-értékek számának csökkentése AD-objektum (15 vagy kevesebb), amelyek már nem a szervezet által használt értékek eltávolításával. Ez a megfelelő, ha az attribútum növekedést lejárt vagy nem használt tanúsítványok okozza. Használhatja a [PowerShell-parancsprogram elérhető itt](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) megtalálásához, biztonsági mentése, és törli a lejárt tanúsítványokat a helyszíni AD. Mielőtt törölné a tanúsítványok, javasoljuk, hogy ellenőrizze a nyilvános-kulcs – infrastruktúra-rendszergazdákkal a szervezetben.

 * A userCertificate attribútum kizárása az Azure AD-exportálás alatt álló Azure AD Connect konfigurálására. Általában nem ajánlott ezt a beállítást, mert az attribútum használható a Microsoft Online Services által meghatározott forgatókönyvek engedélyezhetők. Ebben az esetben:
    * A userCertificate attribútum a User objektum szolgál az Exchange online-hoz és az Outlook-ügyfelek üzenet aláíráshoz és titkosításhoz. Ez a funkció kapcsolatos további információkért tekintse meg a cikk [S/MIME-üzenet aláíráshoz és titkosításhoz](https://technet.microsoft.com/library/dn626158(v=exchg.150).aspx).

    * A userCertificate attribútum a számítógép-objektum segítségével az Azure AD tartományhoz csatlakoztatott eszközök csatlakoztatása az Azure AD lehetővé teszi a helyszíni Windows 10-es. Ezen szolgáltatásáról kapcsolatos további tudnivalókért tekintse meg a cikk [csatlakoztatása az Azure AD-tartományhoz csatlakozott eszközökkel, a Windows 10-es élmény](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Exportálás userCertificate attribútum korlátozása végrehajtási szinkronizálási szabály
A userCertificate attribútum által okozott LargeObject hiba elhárításához is meg lehet valósítani egy kimenő szinkronizálási szabály az Azure AD Connect exportálhatók egy **null érték helyett a tényleges értékek objektumok 15-nél több tanúsítvány értékekkel**. Ez a szakasz ismerteti a lépéseket, a szinkronizálási szabály implementálásához szükséges **felhasználói** objektumokat. A lépések itt leírtakon a **forduljon** és **számítógép** objektumokat.

> [!IMPORTANT]
> Értékek korábban már sikeresen exportálva az Azure AD-tanúsítvány exportálása a null érték eltávolítja.

A lépések szerint lehet összegezni:

1. Tiltsa le a szinkronizálásütemező, és ellenőrizze, hogy nincs folyamatban szinkronizálás.
3. Keresse meg a meglévő kimenő szinkronizálási szabály userCertificate attribútum.
4. A szükséges kimenő szinkronizálási szabály létrehozása.
5. Ellenőrizze az új szinkronizálási szabályt egy meglévő objektum LargeObject hiba miatt.
6. Az új szinkronizálási szabály fennmaradó objektumok LargeObject hiba történt a alkalmazni.
7. Ellenőrizze, hogy nem az Azure ad Szolgáltatásba exportálni váró váratlan mértékben.
8. Exportálja a módosításokat az Azure ad-hez.
9. Engedélyezze újra a sync schedulert.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1. lépés Tiltsa le a szinkronizálásütemező, és ellenőrizze, hogy nincs folyamatban lévő szinkronizálás
Győződjön meg arról, szinkronizálás nem kerül sor, amíg Ön közepén egy új szinkronizálási szabály elkerülése érdekében az Azure AD-exportálás alatt álló nem szándékos módosítások végrehajtására. A beépített szinkronizálásütemező letiltása:
1. Az Azure AD Connect-kiszolgálón indítsa el a PowerShell-munkamenetben.

2. Ütemezett szinkronizálás letiltása parancsmag futtatásával: `Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> Az előző lépések az Azure AD Connect a beépített scheduler az újabb verziók (1.1.xxx.x) csak vonatkoznak. Ha az Azure AD Connect Windows Feladatütemezőt használó régebbi verziók (1.0.xxx.x) használ, vagy (közös) saját egyéni scheduler időszakos szinkronizáláshoz indításához használja, ennek megfelelően tiltsa le szeretné.

1. Indítsa el a **Synchronization Service Managert** a KEZDŐ → szinkronizálási szolgáltatás.

1. Nyissa meg a **műveletek** fülre és ellenőrizze, nincs művelet, amelynek állapota *"folyamatban."*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>2. lépés Keresse meg a meglévő kimenő szinkronizálási szabály userCertificate attribútum
Meglévő szinkronizálási szabály, amely engedélyezve és konfigurálva userCertificate attribútum esetén a felhasználói objektumok exportálása az Azure AD lehetnek. Keresse meg a szinkronizálási szabály ismerje meg, hogy a **elsőbbséget** és **Hatókörszűrő** konfiguráció:

1. Indítsa el a **szinkronizálási Szabályszerkesztővel** KEZDŐ → szinkronizálási Szabályszerkesztővel címen.

2. Adja meg a keresési szűrőket a következő értékeket:

    | Attribútum | Érték |
    | --- | --- |
    | Irány |**Kimenő** |
    | MV-objektum típusa |**Személy** |
    | Összekötő |*az Azure AD-összekötő* |
    | Összekötő-objektum típusa |**user** |
    | MV-attribútum |**userCertificate** |

3. OOB (out-of-box) szinkronizálási szabályokat az Azure AD-összekötő használatakor userCertficiate attribútum esetén a felhasználói objektumok exportálása szerezheti vissza a *"Ki az aad-ben – felhasználói ExchangeOnline"* szabály.
4. Jegyezze fel a **elsőbbséget** értékét a szinkronizálási szabály.
5. Válassza ki a szinkronizálási szabályt, és kattintson a **szerkesztése**.
6. Az a *"Fenntartott szabály megerősítő szerkesztése"* felugró párbeszédpanel, kattintson a **nem**. (Ne aggódjon, nem fogjuk bármilyen módosítás a szinkronizálási szabály).
7. A szerkesztési képernyőn válassza ki a **Scoping szűrő** fülre.
8. Jegyezze fel a hatókörének meghatározásához a szűrők beállítását. Ha az OOB-szinkronizálási szabály használ, pontosan kell **két záradékot tartalmazó egy igényfelmérési Szűrőcsoport**, többek között:

    | Attribútum | Művelet | Érték |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Felhasználó |
    | cloudMastered | NOTEQUAL | True (Igaz) |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>3. lépés A szükséges kimenő szinkronizálási szabály létrehozása
Az új szinkronizálási szabályt kell rendelkeznie, ugyanez **Hatókörszűrő** és **nagyobb prioritással** , mint a meglévő szinkronizálási szabályt. Ez biztosítja, hogy az új szinkronizálási szabály ugyanazt az objektumhalmazt tartalmazza, mint a meglévő szinkronizálási szabály vonatkozik, és felülírja a meglévő szinkronizálási szabályt a userCertificate attribútum. A szinkronizálási szabály létrehozása:
1. A szinkronizálási Szabályszerkesztővel, kattintson a **új szabály hozzáadása** gombra.
2. Alatt a **leírása lap**, adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | --- | --- | --- |
    | Name (Név) | *Adjon meg egy nevet* | Például *"Ki az aad-be – egyéni felülbírálás a userCertificate"* |
    | Leírás | *Adjon meg egy leírást* | Például *"UserCertificate attribútummal rendelkezik, több mint 15 értéket, ha exportálása null értékű."* |
    | Csatlakoztatott rendszer | *Válassza ki az Azure AD Connectoron* |
    | Csatlakoztatott rendszer objektum típusa | **user** | |
    | Metaverzum-objektum típusa | **Személy** | |
    | Hivatkozás típusa | **Csatlakozás** | |
    | Sorrend | *1 és 99 közötti számnak választotta* | A szám kiválasztott bármely meglévő szinkronizálási szabály által nem használható, és egy alacsonyabb értékű (és így nagyobb prioritással), mint a meglévő szinkronizálási szabályt. |

3. Nyissa meg a **Scoping szűrő** lapra, és a meglévő szinkronizálási szabály által használt azonos Hatókörszűrő megvalósításához.
4. Hagyja ki a **szabályok csatlakozzon** fülre.
5. Nyissa meg a **átalakítások** fülre kattintva vegyen fel egy új átalakítás konfiguráció a következő használatával:

    | Attribútum | Érték |
    | --- | --- |
    | Folyamat típusát |**Expression** |
    | Célattribútum |**userCertificate** |
    | Adatforrás-attribútum |*A következő kifejezés használata*: `IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Kattintson a **Hozzáadás** a szinkronizálási szabály létrehozása gombra.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>4. lépés Ellenőrizze az új szinkronizálási szabályt egy meglévő objektum LargeObject hiba
Ezzel esetén ellenőrizze, hogy a szinkronizálási szabály létrehozása megfelelően működik, egy meglévő AD-objektum LargeObject hiba más objektumokra való alkalmazása előtt:
1. Nyissa meg a **műveletek** fülre a Synchronization Service Managert.
2. Válassza ki a legutóbbi Exportálás az Azure AD-műveletet, majd kattintson a LargeObject-hibák az objektumok közül.
3.  Az Összekötőtér-objektum tulajdonságai előugró képernyőn kattintson a a **előzetes** gombra.
4. Az előzetes verzió előugró képernyőn válassza ki a **teljes szinkronizálás** kattintson **véglegesítése előzetes**.
5. Zárja be az előnézet képernyő és a Összekötőtér-objektum tulajdonságai képernyő.
6. Nyissa meg a **összekötők** fülre a Synchronization Service Managert.
7. Kattintson a jobb gombbal a **Azure ad-ben** összekötő, és válassza ki **futtatása...**
8. Az összekötő futtatásához előugró ablakban válassza ki a **exportálása** lépést, és kattintson a **OK**.
9. Várjon, amíg befejeződik, majd erősítse meg, nincs további LargeObject hiba ezt az objektumot az Azure Active Directoryba való exportálás.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>5. lépés Az új szinkronizálási szabályt alkalmazni a hátralévő objektumokhoz LargeObject hiba
Ha a szinkronizálási szabály hozzá lett adva, az AD Connectoron egy teljes szinkronizálást lépés futtatásához szükséges:
1. Nyissa meg a **összekötők** fülre a Synchronization Service Managert.
2. Kattintson a jobb gombbal a **AD** összekötő, és válassza ki **futtatása...**
3. Az összekötő futtatásához előugró ablakban válassza ki a **teljes szinkronizálást** lépést, és kattintson a **OK**.
4. Várjon, amíg a teljes szinkronizálás lépés befejezéséhez.
5. Ha egynél több AD-összekötők ismételje meg a fenti lépéseket a fennmaradó AD-összekötők. Általában több összekötőt szükség, ha több helyszíni címtárral rendelkezik.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>6. lépés Ellenőrizze, hogy nem az Azure ad Szolgáltatásba exportálni váró váratlan mértékben
1. Nyissa meg a **összekötők** fülre a Synchronization Service Managert.
2. Kattintson a jobb gombbal a **Azure ad-ben** összekötő, és válassza ki **keresési Összekötőterét**.
3. A keresési Összekötőterét előugró:
    1. Hatókör beállítása **függő exportálás**.
    2. Ellenőrizze az összes 3 jelölőnégyzetéből, beleértve a **Hozzáadás**, **módosítás** és **törlése**.
    3. Kattintson a **keresési** gomb az Azure ad Szolgáltatásba exportálni váró változások az összes objektum visszaadása.
    4. Ellenőrizze, hogy nem várt változtak. A módosítások egy adott objektum vizsgálatához kattintson duplán az objektumot.

### <a name="step-7-export-the-changes-to-azure-ad"></a>7. lépés A módosítások exportálása az Azure ad-hez
A módosítások exportálása az Azure ad-hez:
1. Nyissa meg a **összekötők** fülre a Synchronization Service Managert.
2. Kattintson a jobb gombbal a **Azure ad-ben** összekötő, és válassza ki **futtatása...**
4. Az összekötő futtatásához előugró ablakban válassza ki a **exportálása** lépést, és kattintson a **OK**.
5. Várjon, amíg befejeződik, majd ellenőrizze, hogy nincsenek további LargeObject-hibák az Azure Active Directoryba való exportálás.

### <a name="step-8-re-enable-sync-scheduler"></a>8. lépés Szinkronizálásütemező újbóli engedélyezése
Most, hogy a probléma megoldódik, engedélyezze újra a beépített szinkronizálásütemező:
1. Indítsa el a PowerShell-munkamenetben.
2. Ütemezett szinkronizálás újra engedélyezéséhez a parancsmag futtatásával: `Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> Az előző lépések az Azure AD Connect a beépített scheduler az újabb verziók (1.1.xxx.x) csak vonatkoznak. Ha az Azure AD Connect Windows Feladatütemezőt használó régebbi verziók (1.0.xxx.x) használ, vagy (közös) saját egyéni scheduler időszakos szinkronizáláshoz indításához használja, ennek megfelelően tiltsa le szeretné.

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).

