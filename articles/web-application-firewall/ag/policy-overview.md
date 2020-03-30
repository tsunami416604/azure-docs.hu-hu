---
title: Az Azure webalkalmazás-tűzfal (WAF) házirendjének áttekintése
description: Ez a cikk a WEBApplication Firewall (WAF) globális, helyenkénti és URI-nkénti házirendjeinek áttekintése.
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: fff4fb5707c07098fd7fac261a36909224365cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060272"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Az Azure webalkalmazás-tűzfal (WAF) házirendjének áttekintése

A webalkalmazás tűzfalházirendjei tartalmazzák az összes WAF-beállítást és -konfigurációt. Ez magában foglalja a kizárásokat, az egyéni szabályokat, a felügyelt szabályokat és így tovább. Ezek a házirendek ezután egy alkalmazásátjáróhoz (globális), egy figyelőhöz (webhelyenként) vagy egy elérési útnivalóra (URI-nként) társítva vannak, hogy érvénybe lépjenek.

> [!NOTE]
> Az Azure Web Application Firewall (WAF) webhelyenként és URI-nkénti szabályzatok nyilvános előzetes verzióban vannak.
> 
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Nincs korlátozva a létrehozható házirendek száma. Amikor létrehoz egy házirendet, azt egy alkalmazásátjáróhoz kell társozni, hogy érvénybe lépjen. Az alkalmazásátjárók, figyelők és az elérési út alapú szabályok bármilyen kombinációjával társítható.

## <a name="global-waf-policy"></a>Globális WAF-politika

Ha globálisan társít waf-szabályzatot, az Application Gateway WAF mögött minden webhely ugyanazokat a felügyelt szabályokat, egyéni szabályokat, kizárásokat és minden más konfigurált beállítást védi.

Ha azt szeretné, hogy egyetlen házirend legyen az összes webhelyre, társíthatja a házirendet az alkalmazásátjáróhoz. További információ: [Webapplication Firewall házirendek létrehozása az Application Gateway](create-waf-policy-ag.md) létrehozásához és alkalmazásához waf-szabályzat az Azure Portalon. 

## <a name="per-site-waf-policy"></a>Webhelyenkénti WAF-házirend

A webhelyenkénti WAF-házirendek segítségével több, eltérő biztonsági igényekkel rendelkező hely védheti meg egyetlen WAF-ot a webhelyenkénti házirendek használatával. Ha például a WAF mögött öt hely van, öt külön WAF-házirend (mindegyik figyelőhöz egy) rendelkezhet a kizárások, az egyéni szabályok, a felügyelt szabálykészletek és az egyes helyek összes többi WAF-beállítás testreszabásához.

Tegyük fel, hogy az alkalmazásátjáró globális szabályzatot alkalmaz. Ezután egy másik szabályzatot alkalmaz egy figyelőaz adott alkalmazásátjárón. A hallgató politikája most lép hatályba, hogy csak a hallgató. Az alkalmazásátjáró globális szabályzata továbbra is vonatkozik az összes többi figyelők és az elérési út-alapú szabályok, amelyek nem rendelkeznek egy adott szabályzat hozzájuk rendelt.

## <a name="per-uri-policy"></a>URI-nkénti házirend

Az URI-szintig még több testreszabásérdekében a WAF-házirendet egy elérési útalapú szabályhoz társíthatja. Ha egy helyen belül vannak olyan lapok, amelyek különböző házirendeket igényelnek, módosíthatja a WAF-házirendet, amely csak egy adott URI-t érint. Ez vonatkozhat egy fizetési vagy bejelentkezési lapra, vagy bármely más URI-ra, amelynek még specifikusabb WAF-házirendre van szüksége, mint a WAF mögött álló többi webhely.

A webhelyenkénti WAF-házirendekhez, a konkrétabb házirendek is felülbírálják a kevésbé specifikusakat. Ez azt jelenti, hogy az URL-elérési út térképén lévő URI-házirend felülírja a felette lévő webhelyeket vagy globális WAF-házirendeket.

## <a name="example"></a>Példa

Tegyük fel, hogy három webhelye van: contoso.com, fabrikam.com és adatum.com ugyanazon alkalmazásátjáró mögött. Azt szeretné, hogy a WAF mindhárom webhelyre alkalmazva legyen, de nagyobb biztonságra van szüksége adatum.com mert az ügyfelek itt látogatják meg, böngészheti kontőket és vásárolnak termékeket.

Alkalmazhat egy globális házirendet a WAF-re, néhány alapvető beállítással, kizárással vagy egyéni szabállyal, ha szükséges, hogy leállítsa a forgalom blokkolását. Ebben az esetben nincs szükség globális SQL-injektálási szabályok futtatására, mert fabrikam.com és contoso.com statikus oldalak sql háttérrendszer nélkül. Így letilthatja ezeket a szabályokat a globális házirendben.

Ez a globális szabályzat alkalmas contoso.com és fabrikam.com, de óvatosabbnak kell lennie a adatum.com ahol a bejelentkezési információk és a kifizetések kezelése történik. Alkalmazhat egy helyenkénti házirendet az adatum figyelőre, és az SQL-szabályok at futtathatja. Azt is tételezzük fel, hogy egy cookie blokkolja a forgalmat, így kizárást hozhat létre az adott cookie-hoz, hogy leállítsa a hamis pozitív. 

Az URI adatum.com/payments az, ahol óvatosnak kell lennie. Ezért alkalmazzon egy másik házirendet az URI-ra, és hagyja az összes szabályt engedélyezve, és távolítsa el az összes kizárást.

Ebben a példában két webhelyre vonatkozó globális szabályzattal rendelkezik. Van egy helyenkénti házirendje, amely egy webhelyre vonatkozik, majd egy URI-nkénti házirend, amely egy adott elérési útalapú szabályra vonatkozik. Lásd (hivatkozás beszúrása itt, ha létezik) hogyan hozhat létre per-site és per-URI szabályzatok a megfelelő PowerShell ebben a példában.

## <a name="existing-waf-configurations"></a>Meglévő WAF-konfigurációk

A WebApplication Firewall összes WAF-beállítása (egyéni szabályok, felügyelt szabálykészlet-konfigurációk, kizárások és így tovább.) létezik a WAF-házirendben. Ha már rendelkezik WAF-fel, ezek a beállítások továbbra is létezhetnek a WAF-konfigurációban. Az új WAF-házirendre való áttérésről a [WAF Config áttelepítése WAF-házirendbe](https://docs.microsoft.com/azure/web-application-firewall/ag/migrate-policy)című további információkért. 


## <a name="next-steps"></a>További lépések

Az Azure PowerShell használatával webhelyenkénti és URI-nkénti szabályzatokat hozhat létre.
