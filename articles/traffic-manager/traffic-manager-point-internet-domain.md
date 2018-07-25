---
title: Vállalati internetes tartomány átirányítása Traffic Manager szolgáltatásbeli tartománynévre | Microsoft Docs
description: Ez a cikk segít átirányítani a vállalata tartománynevét egy Traffic Manager szolgáltatásbeli tartománynevére.
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: 29822946-2d45-4434-ba47-fc180a445cc3
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
ms.openlocfilehash: 63a78332742dcefab20401575b1df0e23c06aa78
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035146"
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
