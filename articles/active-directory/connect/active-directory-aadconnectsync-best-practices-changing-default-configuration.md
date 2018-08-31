---
title: 'Az Azure AD Connect szinkronizálása: az alapértelmezett konfiguráció módosításának |} A Microsoft Docs'
description: Az Azure AD Connect szinkronizálása az alapértelmezett konfiguráció módosításának ajánlott eljárásokat biztosít.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 7638a031-1635-4942-94c3-fce8f09eed5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 0668eb33fe33b062c941ec4f2bff47c5ed77fb51
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287884"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Az Azure AD Connect szinkronizálása: ajánlott eljárások az alapértelmezett konfiguráció módosítása
Ez a témakör az a célja, hogy támogatott és nem támogatott az Azure AD Connect szinkronizálási módosításait ismertetik.

Az Azure AD Connect által létrehozott "formájában is működik", amely a helyszíni Active Directory szinkronizálását az Azure AD legtöbb környezet számára. Azonban bizonyos esetekben, szükség egy adott lenne szükség, vagy a követelmény teljesítéséhez konfiguráció egyes módosítások alkalmazásához.

## <a name="changes-to-the-service-account"></a>A szolgáltatás fiók módosítása
Az Azure AD Connect szinkronizálása a telepítési varázsló által létrehozott szolgáltatásfiók alatt fut. Ez a szolgáltatás fiók tárolja a titkosítási kulcsokat a sync által használt adatbázis. A létrehozást és 127 karakter hosszú jelszót, és a jelszó nem járnak le van állítva.

* Ez **nem támogatott** módosítása vagy a fiók jelszavának alaphelyzetbe állítása. Így megsemmisít a titkosítási kulcsokat és a szolgáltatás nem tud hozzáférni az adatbázishoz, és nem sikerül elindítani.

## <a name="changes-to-the-scheduler"></a>Az ütemező módosításai
A kiadások, a build 1.1 kezdve (2016. február) konfigurálhatja a [scheduler](active-directory-aadconnectsync-feature-scheduler.md) szeretné, hogy egy másik szinkronizálási ciklust, mint az alapértelmezett 30 perc.

## <a name="changes-to-synchronization-rules"></a>Szinkronizálási szabályok módosítása
A telepítővarázsló biztosít egy konfigurációs fájlját a leggyakoribb forgatókönyvek esetén működik. Abban az esetben módosítania kell a konfigurációt, hogy ezek a szabályok továbbra is fennáll egy támogatott konfigurációra kell követnie.

> [!WARNING]
> Ha módosítja az alapértelmezett szinkronizálási szabályok majd ezeket a módosításokat felülírja a következő frissítésekor az Azure AD Connect, nem várt és nagy valószínűséggel nem kívánt szinkronizálási eredmények eredményez.

* Is [attribútumfolyamok módosítása](active-directory-aadconnectsync-change-the-configuration.md#other-common-attribute-flow-changes) , ha az alapértelmezett közvetlen attribútumfolyamok nem alkalmasak a szervezet számára.
* Ha azt szeretné, hogy [attribútum nem flow](active-directory-aadconnectsync-change-the-configuration.md#do-not-flow-an-attribute) , és távolítsa el minden meglévő attribútumértékek az Azure ad-ben, akkor hozzon létre egy szabályt, ehhez a forgatókönyvhöz szükséges.
* [Egy nemkívánatos szinkronizálási szabály letiltása](#disable-an-unwanted-sync-rule) annak törlése helyett. A rendszer a frissítés során újra szabály törölve.
* A [az out-of-box szabály módosítása](#change-an-out-of-box-rule), készítsen másolatot az eredeti szabályt kell és out-of-box szabály letiltása. A szinkronizálási Szabályszerkesztő kér, és segítséget.
* Exportálja a szinkronizálási Szabályszerkesztővel használatával egyéni szinkronizálási szabályait. A szerkesztőben egy PowerShell-parancsprogram segítségével könnyedén létrehozhatja a vész-helyreállítási helyzetekre kínál.

> [!WARNING]
> A beépített szinkronizálási szabályokat rendelkezik egy ujjlenyomatot. Ha egy módosítást hajt végre ezeket a szabályokat, az ujjlenyomat nem egyeznek meg. A jövőben, ha a alkalmazni az Azure AD Connect új kiadása próbál problémák léphetnek fel. Csak módosításokat a leírt módon ebben a cikkben.

### <a name="disable-an-unwanted-sync-rule"></a>Egy nemkívánatos szinkronizálási szabály letiltása
Ne törölje az out-of-box szinkronizálási szabály. A rendszer tovább a frissítés során újra.

Bizonyos esetekben a telepítési varázsló által előállított olyan konfigurációt, amely a topológia nem működik. Például ha egy fiók-erőforrás erdő topológiával rendelkezik, de a sémát a fiókerdőben az Exchange-séma ki van bővítve, majd szabályokat az Exchange-hez jönnek létre az erdő és az erőforráserdőben. Ebben az esetben szükséges a szinkronizálási szabály letiltása az Exchange-hez.

![Letiltott szinkronizálási szabály](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

A fenti ábrán a telepítési varázsló talált egy régi Exchange 2003 séma fiókerdőben. A séma kiterjesztése lett hozzáadva, mielőtt az erőforráserdőben jelent meg a Fabrikam repülőmotorjai környezetben. Annak érdekében, nincsenek attribútumai a régi Exchange végrehajtását a rendszer szinkronizálja, a szinkronizálási szabályt le kell tiltani látható módon.

### <a name="change-an-out-of-box-rule"></a>-Out-of-box szabály módosítása
Csak akkor kell megváltoztatnia az out-of-box szabály, amikor meg kell változtatnia a join szabály. Ha egy Attribútumfolyam módosítani szeretné, majd létre kell hoznia egy szinkronizálási szabály a magasabb, mint az out-of-box szabályok. Az egyetlen szabály klónozásához gyakorlatilag szüksége a szabály **az AD - felhasználó csatlakozzon-ből**. Minden más szabállyal, a magasabb prioritású szabályt felül lehet bírálni.

Ha módosítania kell egy out-of-box szabályt, majd kell másolatot készít az out-of-box szabályt és az eredeti szabály letiltása. A klónozott szabály végezze el a módosításokat. A szinkronizálási Szabályszerkesztő elősegíti az ezeket a lépéseket. Amikor megnyit egy out-of-box szabályt, lehetősége lesz ezen a párbeszédpanelen:  
![Box szabály kívül figyelmeztetés](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Válassza ki **Igen** másolatot készítsen a szabály. A klónozott szabály nyitja meg.  
![Klónozott szabály](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

A klónozott erről a szabályról a hatókör, illesztés és átalakítások végezze el a szükséges módosításokat.

## <a name="next-steps"></a>További lépések
**Áttekintő témakör**

* [Az Azure AD Connect szinkronizálása: ismertetése, és testre szabhatja a szinkronizálás](active-directory-aadconnectsync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
