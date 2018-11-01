---
title: Vállalati internetes tartomány átirányítása Traffic Manager szolgáltatásbeli tartománynévre | Microsoft Docs
description: Ez a cikk segít átirányítani a vállalata tartománynevét egy Traffic Manager szolgáltatásbeli tartománynevére.
services: traffic-manager
documentationcenter: ''
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
ms.openlocfilehash: 45fe4fd8511cd1d725275a5a04bd4b6e13eb68f7
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138395"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Vállalati internetes tartomány átirányítása Azure Traffic Manager-tartományra

Amikor Traffic Manager-profilt hoz létre, az Azure automatikusan hozzárendel egy DNS-nevet a profilhoz. A DNS-zónájából való név felhasználásához hozzon létre egy CNAME DNS-rekordot, amely leképezi a Traffic Manager-profiljának tartománynevét. A Traffic Manager tartományneve a Traffic Manager-profil Konfiguráció lapjának **Általános** részében tekinthető meg.

Ha például a `www.contoso.com` nevet szeretné a Traffic Manager `contoso.trafficmanager.net` DNS-neve felé irányítani, az alábbi DNS-erőforrásrekordot kell létrehoznia:

    www.contoso.com IN CNAME contoso.trafficmanager.net

A rendszer ezentúl a *www.contoso.com* tartományhoz érkező összes forgalomkérést átirányítja a *contoso.trafficmanager.net* tartománynak.

> [!IMPORTANT]
> A második szintű tartomány, például a *contoso.com*, nem irányítható á Traffic Manager-tartományra. A DNS-protokollszabványok nem engedélyezik a CNAME-rekordokat a másodlagos szintű tartománynevek esetében.

## <a name="next-steps"></a>További lépések

* [A Traffic Manager útválasztási módszerei](traffic-manager-routing-methods.md)
* [Traffic Manager – Profil letiltása, engedélyezése vagy törlése](disable-enable-or-delete-a-profile.md)
* [Traffic Manager – Végpont letiltása vagy engedélyezése](disable-or-enable-an-endpoint.md)
