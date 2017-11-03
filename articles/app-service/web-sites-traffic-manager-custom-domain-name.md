---
title: "A webalkalmazás egyéni tartománynév beállítása az Azure App Service által használt Traffic Manager terheléselosztás."
description: "Adjon nevet az egyéni tartomány egy egy webalkalmazást az Azure App Service szolgáltatásban, amely tartalmazza a Traffic Manager a(z) terheléselosztást."
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.openlocfilehash: 5f099201d9018a6f8577cb3daf127d09560fb94b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>A webalkalmazáshoz tartozó egyéni tartománynév konfigurálása az Azure App Service szolgáltatásban a Traffic Managerrel
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Ez a cikk általános útmutatást nyújt az Azure App Service egy egyéni tartománynév használatával, amely a Traffic Manager használata terheléselosztáshoz.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>DNS-rekordok ismertetése
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>A szabványos mód a webalkalmazások konfigurálása
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Az egyéni tartomány DNS-rekord hozzáadása
> [!NOTE]
> Ha tartományi Azure App Service Web Apps keresztül vásárolt, majd ugorjon a következő lépéseket és az utolsó lépésében hivatkozik [tartomány vásárlása Web Apps](custom-dns-web-site-buydomains-web-app.md) cikk.
> 
> 

Az egyéni tartomány társítandó egy webalkalmazást az Azure App Service-ben, hozzá kell adnia egy új bejegyzést a DNS-tábla az egyéni tartomány a tartományregisztráló a tartománynév, a megvásárolt által biztosított eszközök segítségével. Az alábbi lépések segítségével keresse meg és a DNS-eszközök.

1. Jelentkezzen be fiókjába a tartományregisztrálónál, és keresse meg a DNS-rekordok kezelése lapján. Keressen hivatkozásokat vagy a hely címkézve területéhez **tartománynév**, **DNS**, vagy **neve kezelő**. Ezen a lapon egy hivatkozást található gyakran kell a fiók adatainak megtekintésekor, majd egy hivatkozást, mint **a tartományok**.
2. Miután megtalálta a felügyelet lapon a tartománynév, keresse meg a hivatkozást, amellyel lehetővé teszi a DNS-rekordjainak szerkesztését. Ez lehet, hogy legyen felsorolva egy **zónafájl**, **DNS-rekordok**, vagy mint egy **speciális** konfigurációs hivatkozására.
   
   * Az oldalon nagy valószínűséggel kell néhány rekord már létrehozott, például egy bejegyzés társítása "**@**"vagy"\*" "tartomány várakozást" lapot. Például közös altartományok rekordjait is tartalmazhat **www**.
   * A lap fog említse meg **CNAME rekordok**, vagy adjon meg egy legördülő listán válasszon egy rekordtípust. Azt is tüntethető fel más bejegyzések például **A rekordok** és **MX-rekordok**. Bizonyos esetekben a CNAME-rekordok fogja meghívni az egyéb, mint egy **aliasrekordot**.
   * A lap is, amely lehetővé teszi mezők **térkép** a egy **állomásnév** vagy **tartománynév** egy másik tartománynévvel.
3. Minden tartományregisztráló a mintaadatokról eltérők lehetnek, amíg általában leképez *a* az egyéni tartománynevet (például **contoso.com**,) *való* a Traffic Manager szolgáltatásbeli tartománynevére (**contoso.trafficmanager.net**) használt a webalkalmazás.
   
   > [!NOTE]
   > Azt is megteheti Ha egy olyan rekordot már használatban van, és megelőző jelleggel kötni az alkalmazások azt szeretné, létrehozhat egy további CNAME-rekordot. Ahhoz például, hogy megelőző jelleggel kötési **www.contoso.com** a webes alkalmazás, hozzon létre egy CNAME rekordot a **awverify.www** való **contoso.trafficmanager.net**. A webalkalmazás a "www" CNAME rekord módosítása nélkül Ezután felvehet "www.contoso.com". További információkért lásd: [hozzon létre DNS-rekordok az egyéni tartomány webalkalmazáshoz][CREATEDNS].
   > 
   > 
4. Miután befejezte a hozzáadása vagy módosítása a regisztráló DNS-rekordokat, a módosítások mentéséhez.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>A Traffic Manager engedélyezése
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Következő lépések
További információk: [Node.js fejlesztői központ](/develop/nodejs/).

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
