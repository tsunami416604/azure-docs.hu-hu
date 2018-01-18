---
title: "Az Azure AD Connect - userCertificate attribútum által okozott LargeObject hibákat |} Microsoft Docs"
description: "Ez a témakör a javítási lépéseket userCertificate attribútum által okozott LargeObject hibákat."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 73c79e26b2962368f33bbb0d52d6c243b93a3026
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Azure AD Connect szinkronizálása: userCertificate attribútum által okozott kezelése LargeObject hibák

Az Azure AD érvénybe lépteti a jelenlegi maximális műveletszámot **15** értékek a tanúsítvány a **userCertificate** attribútum. Az Azure AD Connect több mint 15 értékekkel rendelkező objektum exportálja az Azure AD, az Azure AD vissza egy **LargeObject** hiba történt a következő üzenetet:

>*"A telepített objektum mérete túl nagy. Trim a számát ezen az objektumon. A műveletet ismét megkísérli a következő szinkronizálási ciklusban..."*

A LargeObject hiba okozhatja más AD attribútumok. Ellenőrizze, valóban okozta a userCertificate attribútum, vissza kell igazolnia, vagy a helyszíni AD-objektuma alapján, vagy a a [Synchronization Service Managert Metaverzum-keresés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-mvsearch).

Szerezze be a bérlő LargeObject hibákkal objektumok listáját, használja a következő módszerek egyikét:

 * Ha a bérlő az Azure AD Connect Health szinkronizálási szolgáltatás engedélyezve van, olvassa el a [szinkronizálási esetleges hibajelentésben való megjelenítéshez](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-sync#object-level-synchronization-error-report-preview) megadott.
 
 * Az egyes szinkronizálási ciklusok végén küldött értesítő e-mail a címtár-szinkronizálási hibák rendelkezik LargeObject hibákkal objektumok listáját. 
 * A [Synchronization Service Manager Operations lapon](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-operations) LargeObject hibákkal objektumok listáját jeleníti meg, ha az Azure AD művelet legújabb exportálás kattint.
 
## <a name="mitigation-options"></a>Megoldás beállításai
Csak a LargeObject hiba megoldása után, más Attribútummódosítások ugyanazon az objektumon nem lehet exportálni az Azure AD. A hiba megoldása érdekében vegye figyelembe az alábbiakat:

 * Frissítse az Azure AD Connect 1.1.524.0 létrehozásához vagy után. Az Azure AD Connectben build 1.1.524.0, out-of-box szinkronizálási szabályok nem exportálhatja a attribútumok userCertificate és userSMIMECertificate Ha az attribútumok több mint 15 értéke frissítve lett. Az Azure AD Connect frissítése a részletekért tekintse meg a cikk [az Azure AD Connect: frissítés egy korábbi verziójáról a legújabb](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

 * Alkalmazzon egy **kimenő szinkronizálási szabályt** az Azure AD Connectben exportálhatók egy **null érték helyett a tényleges értékek objektumok több mint 15 tanúsítvány értékekkel**. Ez a beállítás akkor megfelelő, ha nincs szüksége valamely az Azure AD-objektumok több mint 15 értékekkel exportálandó tanúsítványt értékét. A szinkronizálási szabály megvalósításához további tájékoztatást a következő szakaszban [megvalósítása szinkronizálási szabály, amely korlátozza az Exportálás userCertificate attribútum](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Adjon meg kevesebb tanúsítvány értékek a helyszíni AD-objektum (legfeljebb 15), amelyeket már nem használja a szervezet értékek eltávolításával. Ez a megfelelő, ha az attribútum túlzott növekedést lejárt vagy nem használt tanúsítványok. Használhatja a [PowerShell parancsfájl érhető el itt](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) megtalálásához, biztonsági mentését, és törli a lejárt tanúsítványokat a helyszíni AD. Mielőtt törölné a tanúsítványokat, javasoljuk, hogy ellenőrizze a nyilvános kulcs-infrastrukturális rendszergazdákkal a szervezetében.

 * Ha szeretne kizárni a userCertificate attribútum az Azure AD-exportálás alatt álló Azure AD Connect konfigurálására. Általában nem ajánlott ezt a beállítást, mert az attribútum előfordulhat, hogy használja a Microsoft Online Services által meghatározott forgatókönyvek engedélyezhetők. Ebben az esetben:
    * A userCertificate attribútum a User objektum használják az Exchange Online és az Outlook ügyfelek üzenet aláíráshoz és titkosításhoz. Ezzel a funkcióval kapcsolatban további tudnivalókért tekintse meg a cikk [S/MIME, az üzenet aláírási és titkosítási](https://technet.microsoft.com/library/dn626158(v=exchg.150).aspx).

    * A számítógép-objektum a userCertificate attribútum használatos Azure ad lehetővé teszi a helyszíni Windows 10-es tartományhoz csatlakoztatott eszközök csatlakoztatása az Azure AD. Ezzel a funkcióval kapcsolatban további tudnivalókért tekintse meg a cikk [tartományhoz csatlakozó eszközök csatlakoztatása az Azure AD, a Windows 10 észlel](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Exportálás userCertificate attribútum korlátozni végrehajtási szinkronizálási szabály
A userCertificate attribútum által okozott LargeObject hiba elhárítása érdekében az Azure AD Connectben által is létrehozható egy kimenő szinkronizálási szabályt egy **null érték helyett a tényleges értékek objektumok több mint 15 tanúsítvány értékekkel**. Ez a szakasz ismerteti a szinkronizálási szabály a végrehajtásához szükséges lépéseket **felhasználói** objektumok. A lépéseket a hozzáigazítható **forduljon** és **számítógép** objektumok.

> [!IMPORTANT]
> A tanúsítvány értékek korábban már sikeresen exportálva az Azure AD-exportáló null értéket eltávolítja.

A lépéseket összegezhető:

1. Tiltsa le a szinkronizálásütemező, és ellenőrizze, hogy nincs folyamatban szinkronizálás.
3. A meglévő kimenő szinkronizálási szabály található userCertificate attribútum.
4. A szükséges kimenő szinkronizálási szabály létrehozása.
5. Ellenőrizze az új szinkronizálási szabály LargeObject hibával objektum.
6. Szabály alkalmazása az új szinkronizálási LargeObject hibával hátralévő objektumokhoz.
7. Ellenőrizze, hogy nincsenek az Azure ad Szolgáltatásba exportálni váró váratlan módosítások.
8. Exportálja a módosításokat az Azure AD.
9. Engedélyezze újra a szinkronizálásütemező.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1. lépés Tiltsa le a szinkronizálásütemező, és ellenőrizze, hogy nincs folyamatban lévő szinkronizálás
Győződjön meg arról, szinkronizálás nem történik meg, amíg nem szándékolt módosításokat az Azure AD-exportálás alatt álló elkerülése érdekében új szinkronizálási szabály végrehajtási közepén. A beépített szinkronizálásütemezési letiltása:
1. Indítsa el a PowerShell-munkamenetben az Azure AD Connect-kiszolgálón.

2. Tiltsa le az ütemezett szinkronizálás parancsmag futtatásával:`Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> Az előző lépések az Azure AD Connect a beépített ütemezési és újabb verziók (1.1.xxx.x) csak vonatkoznak. Ha (általános) saját egyéni Feladatütemező segítségével rendszeres szinkronizálás indítható el, vagy régebbi verziójú (1.0.xxx.x) az Azure AD Connect Windows Feladatütemezőt használó használja, ennek megfelelően tiltsa le szeretné.

3. Indítsa el a **Synchronization Service Managert** START → szinkronizálási szolgáltatás címen.

4. Lépjen a **műveletek** lapra, és ellenőrizze, nincs művelet, amelynek állapota *"folyamatban."*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>2. lépés A meglévő kimenő szinkronizálási szabály található userCertificate attribútum
Meglévő szinkronizálási szabály, hogy engedélyezve van, és az Azure AD userCertificate attribútum esetén a felhasználói objektumok exportálása konfigurálva kell lennie. Keresse meg a szinkronizálási szabály megállapítása a **sorrend** és **tartalmazó szűrő** konfigurációs:

1. Indítsa el a **szinkronizálási szabályok szerkesztő** START → szinkronizálási szabályok szerkesztő címen.

2. A keresési szűrők konfigurálására a következő értékekkel:

    | Attribútum | Érték |
    | --- | --- |
    | Irány |**Kimenő** |
    | MV-objektum típusa |**Person** |
    | Összekötő |*az Azure AD-összekötő neve* |
    | Összekötő objektum típusa |**user** |
    | MV-attribútum |**userCertificate** |

3. Ha sávon kívüli (out-of-box) szinkronizálási szabályokat és az Azure AD-összekötő segítségével userCertficiate attribútum esetén a felhasználói objektumok exportálása, kell visszakapnia a *"Ki az aad-ben – felhasználói ExchangeOnline"* szabály.
4. Jegyezze fel a **sorrend** értékét a szinkronizálási szabály.
5. Jelölje ki a szinkronizálási szabályt, és kattintson a **szerkesztése**.
6. Az a *"Fenntartott szabály megerősítő szerkesztése"* előugró párbeszédpanelen kattintson a **nem**. (Ne aggódjon, nem fogjuk módosítja a szinkronizálási szabály).
7. A Szerkesztés szolgáló képernyőn válassza ki a **Scoping szűrő** fülre.
8. Jegyezze fel a tartalmazó szűrő konfigurálása. A sávon kívüli szinkronizálási szabályt használ, ha pontosan kell **egy hatókört szűrő csoportja két záradékok**, többek között a következőket:

    | Attribútum | Operátor | Érték |
    | --- | --- | --- |
    | sourceObjectType | EGYENLŐ | Felhasználó |
    | cloudMastered | NOTEQUAL | True (Igaz) |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>3. lépés A szükséges kimenő szinkronizálási szabály létrehozása
Az új szinkronizálási szabály kell rendelkeznie, ugyanez **tartalmazó szűrő** és **magasabb prioritással** mint a meglévő szinkronizálási szabály. Ez biztosítja, hogy az új szinkronizálási szabály ugyanazokat az objektumok a meglévő szinkronizálási szabály vonatkozik, és felülírja a meglévő szinkronizálási szabály a userCertificate attribútum. A szinkronizálási szabályának létrehozása:
1. Szinkronizálási szabályok szerkesztőben kattintson a **új szabály hozzáadása** gombra.
2. Az a **Leírás lapon**, adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | --- | --- | --- |
    | Name (Név) | *Adjon meg egy nevet* | Például *"Ki az aad-be – egyéni felülírása userCertificate"* |
    | Leírás | *Adjon meg egy leírást* | Például *"UserCertificate attribútum több mint 15 értékkel rendelkezik, ha exportálja NULL."* |
    | Csatlakoztatott rendszer | *Válassza ki az Azure AD Connectoron* |
    | Objektumtípus csatlakoztatva | **user** | |
    | Metaverzum-objektum típusa | **person** | |
    | Kapcsolat típusa | **Csatlakozás** | |
    | Sorrend | *1 és 99 közötti számnak választott* | A szám választott nem kell a meglévő szinkronizálási szabályok által használt, és egy alacsonyabb értékű (és így magasabb prioritással) mint a meglévő szinkronizálási szabály. |

3. Lépjen a **Scoping szűrő** lapra, és ugyanazt a hatókört szűrőt a meglévő szinkronizálási szabály által használt megvalósításához.
4. Ugrás a **szabályok csatlakozás** lapon.
5. Lépjen a **átalakítások** fülre kattintva vegyen fel új átalakítás konfigurációs következő használatával:

    | Attribútum | Érték |
    | --- | --- |
    | Típusa |**Expression** |
    | Célattribútum |**userCertificate** |
    | Adatforrás-attribútum |*A következő kifejezés használata*:`IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Kattintson a **Hozzáadás** a szinkronizálási szabály létrehozása gombra.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>4. lépés Ellenőrizze a LargeObject hiba az objektum az új szinkronizálási szabály
Ez az ellenőrizze, hogy a szinkronizálási szabályt létrehozva megfelelően működik, egy meglévő AD-objektum LargeObject hiba: a más objektumokra alkalmazása előtt:
1. Lépjen a **műveletek** lapon a Synchronization Service Managert.
2. Válassza ki az Azure AD művelet való legutóbbi exportálás, majd kattintson az egyik LargeObject hibákkal objektumot.
3.  Az Összekötőtér-objektum tulajdonságai előugró képernyőn kattintson a a **előzetes** gombra.
4. Az előzetes előugró képernyőn válassza ki a **teljes szinkronizálás** kattintson **véglegesítése előzetes**.
5. Zárja be a nyomtatási kép képernyőjén és a Összekötőtér-objektum tulajdonságai képernyő.
6. Lépjen a **összekötők** lapon a Synchronization Service Managert.
7. Kattintson a jobb gombbal a **az Azure AD** összekötő, és válassza ki **futtatása...**
8. Az összekötő futtatásához előugró ablakban válassza ki a **exportálása** . lépés:, és kattintson a **OK**.
9. Várjon, amíg befejeződik, majd erősítse meg, nincs több LargeObject hiba ezt az objektumot az Azure Active Directoryba való exportálás.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>5. lépés Szabály alkalmazása az új szinkronizálási fennmaradó objektumok LargeObject hiba:
Ha a szinkronizálási szabály hozzá lett adva, az AD Connectoron egy teljes szinkronizálást lépés futtatásához szükséges:
1. Lépjen a **összekötők** lapon a Synchronization Service Managert.
2. Kattintson a jobb gombbal a **AD** összekötő, és válassza ki **futtatása...**
3. Az összekötő futtatásához előugró ablakban válassza ki a **teljes szinkronizálás** . lépés:, és kattintson a **OK**.
4. Várjon, amíg a teljes szinkronizálás lépés befejezéséhez.
5. Ha egynél több AD összekötők ismételje meg a fenti lépéseket a fennmaradó AD összekötők. Általában több összekötő szükség, ha több helyszíni címtárakban.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>6. lépés Ellenőrizze, hogy nincsenek az Azure ad Szolgáltatásba exportálni váró váratlan módosítások
1. Lépjen a **összekötők** lapon a Synchronization Service Managert.
2. Kattintson a jobb gombbal a **az Azure AD** összekötő, és válassza ki **Összekötőtér keresési**.
3. Az Összekötőtér keresési előugró:
    1. Hatókör beállítása **exportálási függőben lévő**.
    2. Ellenőrizze a 3 jelölőnégyzeteket, beleértve a **Hozzáadás**, **módosítás** és **törlése**.
    3. Kattintson a **keresési** gombra kattintva visszaállíthatja az összes objektum az Azure ad Szolgáltatásba exportálni váró módosításokkal.
    4. Ellenőrizze, hogy nincsenek váratlan módosítások. Vizsgálja meg az adott objektum a módosításokat, kattintson duplán az objektum.

### <a name="step-7-export-the-changes-to-azure-ad"></a>7. lépés A módosításokat az Azure AD exportálása
A módosítások exportálása az Azure AD:
1. Lépjen a **összekötők** lapon a Synchronization Service Managert.
2. Kattintson a jobb gombbal a **az Azure AD** összekötő, és válassza ki **futtatása...**
4. Az összekötő futtatásához előugró ablakban válassza ki a **exportálása** . lépés:, és kattintson a **OK**.
5. Várjon, amíg befejeződik, majd erősítse meg a további LargeObject hibák nem találhatók az Azure Active Directoryba való exportálás.

### <a name="step-8-re-enable-sync-scheduler"></a>8. lépés. Engedélyezze újra a szinkronizálásütemező
Most, hogy a probléma megoldódott, engedélyezze újra a beépített szinkronizálásütemezési:
1. Indítsa el a PowerShell-munkamenetben.
2. Újra engedélyezi az ütemezett szinkronizálás parancsmag futtatásával:`Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> Az előző lépések az Azure AD Connect a beépített ütemezési és újabb verziók (1.1.xxx.x) csak vonatkoznak. Ha (általános) saját egyéni Feladatütemező segítségével rendszeres szinkronizálás indítható el, vagy régebbi verziójú (1.0.xxx.x) az Azure AD Connect Windows Feladatütemezőt használó használja, ennek megfelelően tiltsa le szeretné.

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).

