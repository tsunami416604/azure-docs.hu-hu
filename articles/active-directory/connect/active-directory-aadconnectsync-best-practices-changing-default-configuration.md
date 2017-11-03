---
title: "Azure AD Connect szinkronizálása: az alapértelmezett konfiguráció módosításának |} Microsoft Docs"
description: "Ajánlott eljárásokat biztosít az Azure AD Connect szinkronizálási szolgáltatás az alapértelmezett konfiguráció módosításának."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 7638a031-1635-4942-94c3-fce8f09eed5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: b723ad800ccc0f3040eb480bb72960943b1fdb16
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Azure AD Connect szinkronizálása: ajánlott eljárások az alapértelmezett konfiguráció módosítására vonatkozóan
Ez a témakör célja az Azure AD Connect szinkronizálási szolgáltatás támogatott és nem támogatott módosításait ismertetik.

Hozta létre az Azure AD Connect konfigurációs "adott állapotban" működik a legtöbb környezetben, amely a helyszíni Active Directory szinkronizálása az Azure ad-val. Azonban néhány esetben fontos egyes módosítások alkalmazásához egy kifejezetten szüksége vagy követelmény teljesítéséhez konfigurációra.

## <a name="changes-to-the-service-account"></a>A szolgáltatásfiók módosítása
Azure AD Connect szinkronizálása a telepítési varázsló által létrehozott szolgáltatásfiók alatt fut. Ennek a szolgáltatásfióknak a sync által használt adatbázis tárolja a titkosítási kulcsokat. Létrejön egy 127 karakter hosszú jelszót, és a jelszó nem jár le van állítva.

* Az **nem támogatott** módosítása vagy törlése a szolgáltatás fiók jelszavát. Így megsemmisít a titkosítási kulcsok és a szolgáltatás nem tudja elérni az adatbázist, és nincs indul el.

## <a name="changes-to-the-scheduler"></a>Az ütemező módosításai
A kiadást, 1.1-es build a kezdve (2016. februári) is beállíthat a [Feladatütemező](active-directory-aadconnectsync-feature-scheduler.md) kell rendelkeznie az alapértelmezettől eltérő szinkronizálási ciklust 30 perc.

## <a name="changes-to-synchronization-rules"></a>Szinkronizálási szabályok módosítása
A telepítővarázsló felkínálja a konfigurációkat, amelyek működnek a leggyakoribb forgatókönyve az kellene. Abban az esetben, ha módosítania kell a konfigurációt, majd el kell végeznie ezeket a szabályokat még egy támogatott konfigurációra.

* Is [attribútumfolyamok módosítása](active-directory-aadconnectsync-change-the-configuration.md#other-common-attribute-flow-changes) esetén az alapértelmezett közvetlen attribútumfolyamok nem alkalmas a szervezet számára.
* Ha azt szeretné, hogy [attribútum nem flow](active-directory-aadconnectsync-change-the-configuration.md#do-not-flow-an-attribute) , és távolítsa el a meglévő attribútum az Azure AD-értékeket, akkor hozzon létre egy szabályt az ehhez a forgatókönyvhöz szükséges.
* [Egy nem kívánt szinkronizálási szabály letiltása](#disable-an-unwanted-sync-rule) ahelyett, hogy törölné. A törölt szabály jön létre újra a frissítés során.
* A [egy out-of-box szabály módosítása](#change-an-out-of-box-rule), készítsen másolatot az eredeti szabályt, és a out-of-box szabály letiltása. A szinkronizálási szabály szerkesztő kér, és a segítségével.
* Exportálja a a szinkronizálási szabályok szerkesztővel egyéni szinkronizálási szabályait. A szerkesztő segítségével egyszerűen létrehozhatja a vész-helyreállítási forgatókönyv egy PowerShell-parancsfájl segítségével.

> [!WARNING]
> A out-of-box szinkronizálási szabályok rendelkeznek egy ujjlenyomatot. Ha módosítja a ezeket a szabályokat, az ujjlenyomat van már nem megfelelő. Problémák léphetnek fel a jövőben, ha az alkalmazása az Azure AD Connect új verziót. Csak módosításokat leírt módon ebben a cikkben.

### <a name="disable-an-unwanted-sync-rule"></a>Egy nem kívánt szinkronizálási szabály letiltása
Ne törölje az out-of-box szinkronizálási szabály. Az következő frissítés során újból létrejön.

Néhány esetben a varázsló által előállított a konfigurációkat, amelyek a topológia nem működik. Például ha egy fiók-erőforrás szolgának rendelkezik, de a séma a erdőjében, az Exchange-séma már ki van bővítve, majd Exchange vonatkozó szabályok létrehozása a az erdő és az erőforráserdőben. Ebben az esetben szüksége a szinkronizálási szabály letiltása az Exchange-hez.

![Letiltott szinkronizálási szabály](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

A fenti kép a telepítővarázsló talált egy régi Exchange 2003 séma a fiókerdő. A séma kiterjesztése előtt az erőforráserdőben Fabrikam környezetben jelent meg. Annak biztosítása érdekében a régi Exchange végrehajtása nem attribútum szinkronizálva, a szinkronizálási szabályt le kell tiltani látható módon.

### <a name="change-an-out-of-box-rule"></a>Az out-of-box szabály módosítása
A csak meg kell változtatni egy out-of-box szabály, amikor az illesztés szabály módosítani kell. Ha egy Attribútumfolyam módosítani szeretné, majd, hozzon létre egy szinkronizálási szabály magasabb prioritású, mint a out-of-box szabály. A Klónozás gyakorlatilag kell csak szabály a szabály **a az AD - felhasználó csatlakozás**. Minden más szabályaival magasabb elsőbbséget szabályok felülbírálásához.

Ha módosítania kell az out-of-box szabály, majd meg kell készítsen másolatot a out-of-box szabály, letiltja az eredeti szabályt. A klónozott szabály végezze el a módosításokat. A szinkronizálási szabály szerkesztő elősegíti az ezeket a lépéseket. Amikor megnyit egy out-of-box szabály, lehetősége lesz ezen a párbeszédpanelen:  
![Figyelmeztetés kívül mezőben szabály](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Válassza ki **Igen** egy példányát a szabály létrehozásához. A klónozott szabály nyitja meg.  
![Klónozott szabály](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

A klónozott szabály, a beállítások módosításait szükséges hatókör, illesztési és átalakításához.

## <a name="next-steps"></a>Következő lépések
**Áttekintő témakör**

* [Azure AD Connect szinkronizálása: megértéséhez, valamint a szinkronizálás testreszabása](active-directory-aadconnectsync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
