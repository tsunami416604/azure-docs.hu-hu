---
title: Azure AD Connect health – Duplikált attribútumszinkronizálási hibák diagnosztizálása | Microsoft dokumentumok
description: Ez a dokumentum ismerteti a diagnosztikai folyamat duplikált attribútum szinkronizálási hibák és az árva objektum forgatókönyvek lehetséges javítása közvetlenül az Azure Portalon.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: maheshu
editor: billmath
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48ed9abf3e088e2581a3dd81b7c89e6b99da3ceb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897192"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Duplikált attribútumok szinkronizálási hibáinak diagnosztizálása és javítása

## <a name="overview"></a>Áttekintés
Az Azure Active Directory (Azure AD) Connect Health egy lépéssel tovább halad a szinkronizálási hibák kiemelése érdekében. Elhárítja a duplikált attribútumszinkronizálási hibákat, és javítja az Azure AD-ből árván maradt objektumokat.
A diagnosztikai funkció a következő előnyökkel jár:
- Olyan diagnosztikai eljárást biztosít, amely leszűkíti a duplikált attribútumszinkronizálási hibákat. És ez ad konkrét javításokat.
- Egy javítást alkalmaz az Azure AD dedikált forgatókönyveihez a hiba egyetlen lépésben történő megoldásához.
- A szolgáltatás engedélyezéséhez nincs szükség frissítésre vagy konfigurációra.
Az Azure AD-ről további információt az [Identitás-szinkronizálás és az attribútum-rugalmasság duplikált.](how-to-connect-syncservice-duplicate-attribute-resiliency.md)

## <a name="problems"></a>Problémák
### <a name="a-common-scenario"></a>Gyakori forgatókönyv
Amikor **a QuarantinedAttributeValueMustBeUnique** és **az AttributeValueMustBeUnique** szinkronizálási hibák történnek, gyakori, hogy az Azure AD-ben **userprincipalname** vagy **proxycímek ütköznek.** Előfordulhat, hogy a szinkronizálási hibákat az ütköző forrásobjektum helyszíni oldalról való frissítésével oldja meg. A szinkronizálási hiba a következő szinkronizálás után megszűnik. Ez a kép például azt jelzi, hogy két felhasználó nak ütközik a **UserPrincipalName**. Mindkettő **Joe.J.\@contoso.com.** Az ütköző objektumok karanténba kerülnek az Azure AD-ben.

![Szinkronizálási hiba gyakori forgatókönyvének diagnosztizálása](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Árva objektum forgatókönyve
Előfordulhat, hogy egy meglévő felhasználó elveszíti a **Forráshorgonyt.** A forrásobjektum törlése a helyszíni Active Directoryban történt. De a törlési jel módosítása soha nem lett szinkronizálva az Azure AD-vel. Ez a veszteség olyan okok miatt következik be, mint a szinkronizálási motorokkal kapcsolatos problémák vagy a tartomány áttelepítése. Amikor ugyanazt az objektumot visszaállítja vagy újra létrehozza, logikusan egy meglévő felhasználónak kell szinkronizálnia a **forráshorgonyból.** 

Ha egy meglévő felhasználó csak felhőalapú objektum, az ütköző felhasználó is láthatja az Azure AD-vel szinkronizálva. A felhasználó nem párosítható szinkronban a meglévő objektummal. Nincs közvetlen módja a **Forráshorgony**újratérképezésének. További információ a [meglévő tudásbázisról.](https://support.microsoft.com/help/2647098) 

Például a meglévő objektum az Azure AD-ben megőrzi a Joe licencét. Egy újonnan szinkronizált objektum egy másik forrás horgony fordul elő egy duplikált attribútum állapotban az Azure AD.A newly synchronized object with a different **Source Anchor** occurs in a duplicated attribute state in Azure AD. A helyszíni Active Directoryban Joe módosításai nem lesznek alkalmazva Joe eredeti (meglévő objektum) azure AD-ben.  

![Szinkronizálási hiba árva objektumforgatókönyvének diagnosztizálása](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Diagnosztikai és hibaelhárítási lépések a Csatlakozás állapotában 
A diagnosztizálási szolgáltatás a következő duplikált attribútumokkal rendelkező felhasználói objektumokat támogatja:

| Attribútum neve | Szinkronizálási hibatípusok|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique vagy AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique vagy AttributeValueMustBeUnique | 
| SipProxyAddress cím | AttributeValueMustBeUnique | 
| Helyszínibiztonsági azonosító |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> A szolgáltatás eléréséhez **globális rendszergazdai** engedély vagy az RBAC-beállítások **közreműködői** engedélye szükséges.
>

Kövesse az Azure Portal lépéseit a szinkronizálási hiba részleteinek leszűkítéséhez és konkrétabb megoldások biztosításához:

![Szinkronizálási hibadiagnosztikai lépések](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

Az Azure Portalon néhány lépéssel azonosíthatja a konkrét javítható forgatókönyveket:  
1.  Ellenőrizze a **Diagnosztizálás állapota** oszlopot. Az állapot azt mutatja, ha van egy lehetséges módja a szinkronizálási hiba közvetlenül az Azure Active Directoryból. Más szóval létezik egy hibaelhárítási folyamat, amely leszűkítheti a hibaesetet, és potenciálisan javíthatja azt.

| status | Mit jelentsen ez? |
| ------------------ | -----------------|
| Nincs elindítva | Még nem látogatta meg ezt a diagnosztikai folyamatot. A diagnosztikai eredménytől függően van egy lehetséges módja annak, hogy a szinkronizálási hibát közvetlenül a portálról javítsa ki. |
| Kézi javítás szükséges | A hiba nem felel meg a portálon elérhető javítások feltételeinek. Az ütköző objektumtípusok nem felhasználók, vagy már átment a diagnosztikai lépéseken, és nem volt elérhető javításfeloldás a portálon. Az utóbbi esetben a helyszíni oldalon egy javítás továbbra is az egyik megoldás. [További információ a helyszíni javításokról.](https://support.microsoft.com/help/2647098) | 
| Függőben lévő szinkronizálás | A program egy javítást alkalmazott. A portál a következő szinkronizálási ciklusra vár a hiba törléséhez. |

  >[!IMPORTANT]
  > A diagnosztikai állapotoszlop minden szinkronizálási ciklus után alaphelyzetbe áll. 
  >

1. A **hibarészletei** alatt válassza a Diagnosztizálás gombot. Válaszol néhány kérdésre, és azonosítja a szinkronizálási hiba részleteit. A kérdésekre adott válaszok segítenek azonosítani az árva objektumesetet.

1. Ha a diagnosztika végén megjelenik egy **Bezárás** gomb, a válaszok alapján nincs gyors javítás a portálon. Tekintse meg az utolsó lépésben látható megoldást. A helyszíni javítások továbbra is a megoldások. Kattintson a **Bezárás** gombra. Az aktuális szinkronizálási hiba állapota **kézi javításra vált.** Az állapot az aktuális szinkronizálási ciklus ban marad.

1. Az árva objektumeset azonosítását követően a duplikált attribútumok szinkronizálási hibáit közvetlenül a portálról javíthatja ki. A folyamat elindításához válassza a **Javítás alkalmazása** gombot. Az aktuális szinkronizálási hiba állapota **a Függőben lévő szinkronizálás ra**frissül.

1. A következő szinkronizálási ciklus után a hibát el kell távolítani a listáról.

## <a name="how-to-answer-the-diagnosis-questions"></a>Hogyan válaszoljunk a diagnózis kérdéseire? 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Létezik a felhasználó a helyszíni Active Directoryban?

Ez a kérdés megpróbálja azonosítani a meglévő felhasználó forrásobjektumát a helyszíni Active Directoryból.  
1. Ellenőrizze, hogy az Azure Active Directory rendelkezik-e a megadott **UserPrincipalName objektummal.** Ha nem, válaszoljon **a Nem**.
2. Ha igen, ellenőrizze, hogy az objektum továbbra is a szinkronizálás hatókörében van-e.  
   - Keresés az Azure AD-összekötő térben a DN használatával.
   - Ha az objektum a **Függőben lévő hozzáadás** állapotban található, válaszoljon **a Nem**válaszra. Az Azure AD Connect nem tudja csatlakoztatni az objektumot a megfelelő Azure AD-objektumhoz.
   - Ha az objektum nem található, válaszoljon **az Igen gombra.**

Ezekben a példákban a kérdés megpróbálja azonosítani, hogy **Joe Jackson** még mindig létezik-e a helyszíni Active Directoryban.
A **gyakori forgatókönyv**esetén **joe johnson** és joe **jackson** felhasználók egyaránt jelen vannak a helyszíni Active Directoryban. A karanténba helyezett objektumok két különböző felhasználó.

![Szinkronizálási hiba gyakori forgatókönyvének diagnosztizálása](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

Az **árva objektum forgatókönyv esetén**csak az egyetlen felhasználó **Joe Johnson** van jelen a helyszíni Active Directoryban:

![Diagnosztizálása szinkronizálási hiba árva objektum *nem felhasználó létezik * forgatókönyv](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Mindkét fiók ugyanahhoz a felhasználóhoz tartozik?
Ez a kérdés ellenőrzi a bejövő ütköző felhasználó és a meglévő felhasználói objektum az Azure AD-ben, hogy ha ugyanahhoz a felhasználóhoz tartoznak.  
1. Az ütköző objektum újonnan szinkronizálva van az Azure Active Directoryval. Hasonlítsa össze az objektumok attribútumait:  
   - Megjelenítendő név
   - Felhasználó egyszerű neve
   - Objektumazonosító
2. Ha az Azure AD nem tudja összehasonlítani őket, ellenőrizze, hogy az Active Directory rendelkezik-e objektumokat a megadott UserPrincipalNames .If Azure AD fail to compare them, check whether Active Directory has objects with the provided **UserPrincipalNames**. Válasz **Nem,** ha megtalálja mindkettőt.

A következő példában a két objektum ugyanahhoz a joe **johnson**felhasználóhoz tartozik.

![Szinkronizálási hiba árva objektum *azonos felhasználó* forgatókönyvének diagnosztizálása](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Mi történik a javítás alkalmazása után az árva objektum esetén?
Az előző kérdésekre adott válaszok alapján megjelenik a **Javítás alkalmazása** gomb, ha van egy javítás elérhető az Azure AD-ből. Ebben az esetben a helyszíni objektum egy váratlan Azure AD-objektummal szinkronizál. A két objektum leképezése a Forráshorgony használatával lesz **leképezve.** A **Javítás alkalmazása** módosítás az alábbi vagy hasonló lépéseket teszi:
1. Frissíti a **forráshorgonyt** a megfelelő objektumra az Azure AD-ben.
2. Törli az ütköző objektumot az Azure AD-ben, ha az jelen van.

![Szinkronizálási hiba diagnosztizálása a javítás után](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> A **Javítás alkalmazása** módosítás csak az árva objektumesetekre vonatkozik.
>

Az előző lépések után a felhasználó hozzáférhet az eredeti erőforráshoz, amely egy meglévő objektumra mutató hivatkozás. A **listanézet állapotának diagnosztizálása** értéke **a Függőben lévő szinkronizálás**nézetben frissül. A szinkronizálási hiba a következő szinkronizálás után megszűnik. A Connect Health a továbbiakban nem jeleníti meg a megoldott szinkronizálási hibát a listanézetben.

## <a name="failures-and-error-messages"></a>Hibák és hibaüzenetek
**Az ütköző attribútummal rendelkező felhasználó helyreállíthatóan törlődik az Azure Active Directoryban. Győződjön meg arról, hogy a felhasználó tévábban törlődik, mielőtt újra próbálkozna.**  
Az Azure AD-ben ütköző attribútummal rendelkező felhasználót a javítás alkalmazása előtt meg kell tisztítani. Tekintse [meg, hogyan törölheti a felhasználót véglegesen az Azure AD-ben,](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore) mielőtt újra megpróbálkozna a javítással. A felhasználó is automatikusan törlődik véglegesen 30 nap után a helyreállíthatóan törölt állapotban. 

**A forráshorgony frissítése a bérlőben lévő felhőalapú felhasználószámára nem támogatott.**  
Az Azure AD felhőalapú felhasználója nem rendelkezhet forráshorgonysal. Ebben az esetben a forráshorgony frissítése nem támogatott. Kézi javítás szükséges a helyszínen. 

## <a name="faq"></a>GYIK
**K.** Mi történik, ha a **Fix alkalmazása** végrehajtása sikertelen?  
**A.** Ha a végrehajtás sikertelen, lehetséges, hogy az Azure AD Connect exportálási hibát futtat. Frissítse a portállapot, és próbálkozzon újra a következő szinkronizálás után. Az alapértelmezett szinkronizálási ciklus 30 perc. 


**K.** Mi a teendő, ha a **meglévő objektumnak** törölni kell a törölni az objektumot?  
**A.** Ha a **meglévő objektumot** törölni kell, a folyamat nem jár a **Forráshorgony módosításával.** Általában a helyszíni Active Directoryból javíthatja ki. 


**K.** Milyen engedélyre van szüksége a felhasználónak a javítás alkalmazásához?  
**A.** **A Globális rendszergazda**vagy az RBAC-beállítások **közreműködője** jogosult a diagnosztikai és hibaelhárítási folyamat elérésére.


**K.** Konfigurálnom kell az Azure AD Connectet, vagy frissítenem kell az Azure AD Connect health ügynökét ehhez a funkcióhoz?  
**A.** Nem, a diagnosztikai folyamat egy teljes felhőalapú funkció.


**K.** Ha a meglévő objektum helyreállíthatóan törlődik, a diagnosztikai folyamat újra aktívvá teszi az objektumot?  
**A.** Nem, a javítás nem frissíti a **Forrásszerkesztőn**kívül más objektumattribútumokat.
