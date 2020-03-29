---
title: 'Azure AD Connect szinkronizálás: Az alapértelmezett konfiguráció módosítása | Microsoft dokumentumok'
description: Ajánlott eljárások az Azure AD Connect szinkronizálásalapértelmezett konfigurációjának módosításához.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 7638a031-1635-4942-94c3-fce8f09eed5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/29/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 940a35d89996b1eb9600fe4214863d2b5304750e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60242118"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Azure AD Connect szinkronizálása: Gyakorlati tanácsok az alapértelmezett konfiguráció módosításához
Ez a témakör célja az Azure AD Connect szinkronizálástámogatott és nem támogatott módosításai leírása.

Az Azure AD Connect által létrehozott konfiguráció "a hogy van" működik a legtöbb olyan környezetben, amely szinkronizálja a helyszíni Active Directoryt az Azure AD-vel. Bizonyos esetekben azonban egy adott igény vagy követelmény kielégítése érdekében néhány módosítást kell alkalmazni a konfigurációban.

## <a name="changes-to-the-service-account"></a>A szolgáltatásfiók módosításai
Az Azure AD Connect szinkronizálása a telepítővarázsló által létrehozott szolgáltatásfiók alatt fut. Ez a szolgáltatásfiók tartalmazza a szinkronizálás által használt adatbázis titkosítási kulcsait. Jön létre egy 127 karakter hosszú jelszót, és a jelszó van beállítva, hogy nem jár le.

* A szolgáltatásfiók jelszavának módosítása vagy alaphelyzetbe állítása **nem támogatott.** Ezzel elpusztítja a titkosítási kulcsokat, és a szolgáltatás nem tud hozzáférni az adatbázishoz, és nem tud elindítani.

## <a name="changes-to-the-scheduler"></a>Az ütemező módosításai
A build 1.1-es kiadásaival kezdve (2016. február) beállíthatja, hogy az [ütemező](how-to-connect-sync-feature-scheduler.md) az alapértelmezett 30 perctől eltérő szinkronizálási ciklussal rendelkezzen.

## <a name="changes-to-synchronization-rules"></a>A szinkronizálási szabályok módosításai
A telepítővarázsló olyan konfigurációt biztosít, amely a leggyakoribb esetekben is működik. Abban az esetben, ha módosítania kell a konfigurációt, akkor kövesse ezeket a szabályokat, hogy továbbra is támogatott konfigurációval rendelkezik.

> [!WARNING]
> Ha módosítja az alapértelmezett szinkronizálási szabályokat, majd ezeket a módosításokat a rendszer felülírja az Azure AD Connect következő frissítésekor, ami váratlan és valószínűleg nem kívánt szinkronizálási eredményeket eredményez.

* [Módosíthatja az attribútumfolyamatokat,](how-to-connect-sync-change-the-configuration.md#other-common-attribute-flow-changes) ha az alapértelmezett közvetlen attribútumfolyamatok nem megfelelőek a szervezet számára.
* Ha azt szeretné, hogy [ne folytassa az attribútumot,](how-to-connect-sync-change-the-configuration.md#do-not-flow-an-attribute) és távolítsa el a meglévő attribútumértékeket az Azure AD-ben, majd létre kell hoznia egy szabályt ehhez a forgatókönyvhöz.
* Törlés helyett [tiltson le egy nem kívánt szinkronizálási szabályt.](#disable-an-unwanted-sync-rule) A törölt szabály újra létrejön a frissítés során.
* [A beépített szabály módosításához](#change-an-out-of-box-rule)készítsen másolatot az eredeti szabályról, és tiltsa le a beépített szabályt. A Szinkronizálási szabályszerkesztő kéri, és segít.
* Exportálja az egyéni szinkronizálási szabályokat a Szinkronizálási szabályok szerkesztővel. A szerkesztő egy PowerShell-parancsfájlt biztosít, amelyekkel könnyedén újra létrehozhatja őket egy vész-helyreállítási forgatókönyvben.

> [!WARNING]
> A beépített szinkronizálási szabályok ujjlenyomattal rendelkeznek. Ha módosítja ezeket a szabályokat, az ujjlenyomat már nem egyezik. Előfordulhat, hogy a jövőben problémák merülnek fel, amikor megpróbálja alkalmazni az Azure AD Connect új kiadását. Csak a cikkben leírt módon módosítsa a cikket.

### <a name="disable-an-unwanted-sync-rule"></a>Nem kívánt szinkronizálási szabály letiltása
Ne töröljön egy beépített szinkronizálási szabályt. A következő frissítés során újra létrejön.

Bizonyos esetekben a telepítővarázsló olyan konfigurációt hozott létre, amely nem működik a topológiában. Ha például rendelkezik egy fiók-erőforrás erdőtopológiával, de a fiókerdő sémáját kiterjesztette az Exchange-sémával, akkor az Exchange-szabályok jönnek létre a fiókerdőhöz és az erőforráserdőhöz. Ebben az esetben le kell tiltania az Exchange szinkronizálási szabályát.

![Letiltott szinkronizálási szabály](./media/how-to-connect-sync-best-practices-changing-default-configuration/exchangedisabledrule.png)

A fenti képen a telepítővarázsló egy régi Exchange 2003-as sémát talált a fiókerdőben. Ez a sémabővítmény az erőforráserdő Fabrikam környezetében való bevezetése előtt lett hozzáadva. Annak érdekében, hogy a régi Exchange-implementáció egyetlen attribútuma se legyen szinkronizálva, a szinkronizálási szabályt le kell tiltani az ábrán látható módon.

### <a name="change-an-out-of-box-rule"></a>Beépített szabály módosítása
Csak akkor kell módosítania egy beépített szabályt, amikor módosítania kell az illesztési szabályt. Ha módosítania kell egy attribútumfolyamatot, akkor hozzon létre egy szinkronizálási szabályt, amelynek magasabb a prioritása, mint a beépített szabályoknak. Az egyetlen szabály, amit gyakorlatilag klónoznia kell, az **AD - User Join**. Az összes többi szabályt felülbírálhatja magasabb prioritási szabállyal.

Ha módosítania kell egy beépített szabályt, akkor készítsen másolatot a beépített szabályról, és tiltsa le az eredeti szabályt. Ezután hajtsa végre a klónozott szabály módosításait. A szinkronizálási szabályszerkesztő segít a lépések megoldásában. Amikor megnyit egy beépített szabályt, a következő párbeszédpanel jelenik meg:  
![Figyelmeztetés dobozon kívüli szabály](./media/how-to-connect-sync-best-practices-changing-default-configuration/warningoutofboxrule.png)

A szabály másolatának létrehozásához válassza az **Igen** lehetőséget. A klónozott szabály ezután megnyílik.  
![Klónozott szabály](./media/how-to-connect-sync-best-practices-changing-default-configuration/clonedrule.png)

Ebben a klónozott szabály, hogy a szükséges módosításokat a hatókör, illesztés, és átalakítások.

## <a name="next-steps"></a>További lépések
**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: A szinkronizálás megértése és testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
