---
title: Felkészülés az SSL IP-címének módosítására
description: Ha az SSL-IP-cím meg fog változni, olvassa el, hogy mi a teendő, hogy az alkalmazás a módosítás után is működjön.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: dcfe11bcab25f6267a557de5faf7befab467bc29
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535723"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>Felkészülés az SSL IP-címváltozásra

Ha értesítést kapott arról, hogy az Azure App Service-alkalmazás SSL-IP-címe változik, kövesse a cikkben található utasításokat a meglévő SSL-IP-cím felszabadításához és egy új hozzárendeléséhez.

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>Ssl IP-címek kiadása és újak hozzárendelése

1.  Nyissa meg az [Azure Portalt](https://portal.azure.com).

2.  A bal oldali navigációs menüben válassza az **App Services**lehetőséget.

3.  Válassza ki az App Service-alkalmazást a listából.

4.  A **Beállítások** fejléc alatt kattintson az **SSL-beállítások** elemre a bal oldali navigációs sávon.

1. A TLS/SSL kötések szakaszban jelölje ki az állomásnév-rekordot. A megnyíló szerkesztőben válassza az **SSL-típus** legördülő menü **SNI SSL** parancsát, majd kattintson a **Kötés hozzáadása**parancsra. Amikor megjelenik a művelet sikeres üzenet, a meglévő IP-cím megjelent.

6.  Az **SSL-kötések** szakaszban ismét jelölje ki ugyanazt az állomásnév-rekordot a tanúsítvánnyal. A megnyíló szerkesztőben ezúttal válassza az **IP-alapú SSL lehetőséget** az **SSL típus** legördülő menüben, majd kattintson a **Kötés hozzáadása**parancsra. Amikor megjelenik a művelet sikeres üzenet, új IP-címet szerzett be.

7.  Ha egy A rekord (közvetlenül az IP-címre mutató DNS-rekord) van konfigurálva a domain regisztrációs portálon (külső DNS-szolgáltató vagy Az Azure DNS), cserélje le a meglévő IP-címet az újonnan létrehozott. Az új IP-címet a következő szakaszban található utasításokat követve találhatja meg.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Az új SSL-IP-cím megkeresése az Azure Portalon

1.  Várjon néhány percet, majd nyissa meg az [Azure Portalt.](https://portal.azure.com)

2.  A bal oldali navigációs menüben válassza az **App Services**lehetőséget.

3.  Válassza ki az App Service-alkalmazást a listából.

4.  A **Beállítások** fejléc alatt kattintson a bal oldali navigációs sáv **Tulajdonságok gombjára,** és keresse meg a **Virtuális IP-cím**feliratú szakaszt.

5. Másolja az IP-címet, és konfigurálja újra a tartományrekordot vagy az IP-mechanizmust.

## <a name="next-steps"></a>További lépések

Ez a cikk ismerteti, hogyan készülhet fel az Azure által kezdeményezett IP-címmódosításra. Az Azure App Service IP-címeiről további információt a [Bejövő és kimenő IP-címek az Azure App Service-ben című témakörben talál.](overview-inbound-outbound-ips.md)
