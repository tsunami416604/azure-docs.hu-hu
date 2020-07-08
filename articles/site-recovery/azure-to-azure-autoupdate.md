---
title: A mobilitási szolgáltatás automatikus frissítése Azure Site Recovery
description: A mobilitási szolgáltatás automatikus frissítésének áttekintése az Azure-beli virtuális gépek Azure Site Recovery használatával történő replikálása során.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: rajanaki
ms.openlocfilehash: b2f5faea3df695500ea245d1dc71cb96a84c3643
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985601"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>A mobilitási szolgáltatás automatikus frissítése az Azure-ról az Azure-ba történő replikációra

Azure Site Recovery a havi kiadási lépésszám segítségével elháríthatja a problémákat, és javíthatja a meglévő szolgáltatásokat, illetve újakat vehet fel. Ha továbbra is meg szeretné őrizni a szolgáltatást, minden hónapban meg kell terveznie a javítások telepítését. Az egyes frissítésekhez kapcsolódó terhelés elkerülése érdekében engedélyezheti Site Recovery számára az összetevők frissítését.

Az [Azure – Azure vész-helyreállítási architektúrában](azure-to-azure-architecture.md)említettek szerint a mobilitási szolgáltatás minden olyan Azure-beli virtuális gépre (VM) telepítve van, amelyen engedélyezve van az egyik Azure-régióból a másikba való replikáció. Ha automatikus frissítéseket használ, minden új kiadás frissíti a mobilitási szolgáltatás bővítményét.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Az automatikus frissítések működése

Ha a Site Recovery használatával kezeli a frissítéseket, az az Azure-szolgáltatások által használt globális runbook helyez üzembe egy Automation-fiókon keresztül, amely ugyanabban az előfizetésben jön létre, mint a tároló. Mindegyik tároló egy Automation-fiókot használ. A tárolóban lévő minden egyes virtuális gép esetében a runbook ellenőrzi az aktív automatikus frissítéseket. Ha a mobilitási szolgáltatás bővítményének újabb verziója érhető el, a rendszer telepíti a frissítést.

Az alapértelmezett runbook-ütemterv napi rendszerességgel 12:00 ÓRAKOR történik a replikált virtuális gép Földrajzának időzónájában. A runbook-ütemtervet az Automation-fiók használatával is módosíthatja.

> [!NOTE]
> A 35-es [kumulatív frissítéstől](site-recovery-whats-new.md#updates-march-2019)kezdve kiválaszthatja a frissítésekhez használandó meglévő Automation-fiókot. A 35-es kumulatív frissítés előtt a Site Recovery alapértelmezés szerint létrehozta az Automation-fiókot. Ezt a beállítást csak akkor válassza ki, ha engedélyezi egy virtuális gép replikálását. Nem érhető el olyan virtuális géphez, amelyen már engedélyezve van a replikáció. A kiválasztott beállítás az azonos tárolóban védett összes Azure-beli virtuális gépre vonatkozik.

Az automatikus frissítések bekapcsolása nem igényli az Azure-beli virtuális gépek újraindítását, vagy a folyamatban lévő replikációt is befolyásolja.

Az Automation-fiókban a feladatok számlázása a hónap során felhasznált feladatok futtatókörnyezeti percének számától függ. A feladatok végrehajtása naponta körülbelül egy percet vesz igénybe, és ingyenes egységként van lefoglalva. Alapértelmezés szerint a 500 perc egy Automation-fiók ingyenes egysége, ahogy az az alábbi táblázatban is látható:

| A csomagban foglalt ingyenes egységek (havonta) | Price |
|---|---|
| Feladatok futtatókörnyezete 500 perc | ₹ 0.14/perc

## <a name="enable-automatic-updates"></a>Automatikus frissítések engedélyezése

A bővítmény frissítéseinek Site Recovery többféleképpen is kezelhetők:

- [Kezelés a replikálás engedélyezése lépés részeként](#manage-as-part-of-the-enable-replication-step)
- [A bővítmény frissítési beállításainak váltása a tárolón belül](#toggle-the-extension-update-settings-inside-the-vault)
- [Frissítések manuális kezelése](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Kezelés a replikálás engedélyezése lépés részeként

Ha engedélyezi egy virtuális gép replikálását a virtuálisgép- [nézetből](azure-to-azure-quickstart.md) vagy [a Recovery Services](azure-to-azure-how-to-enable-replication.md)-tárolóból, engedélyezheti site Recovery számára a site Recovery bővítmény frissítéseinek kezelését, vagy manuálisan is kezelheti azt.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Bővítmény beállításai":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>A bővítmény frissítési beállításainak váltása a tárolón belül

1. A Recovery Services-tárolóban lépjen a **Manage**  >  **site Recovery infrastruktúra**kezelése elemre.
1. **Az Azure Virtual Machines**  >  **Extension Update Settings**(az Azure-ban) lehetőségnél  >  **engedélyezze a site Recovery kezelését**, válassza **a be**lehetőséget.

   Ha manuálisan szeretné kezelni a bővítményt, válassza a **ki**lehetőséget.

1. Kattintson a **Mentés** gombra.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Bővítmény frissítési beállításai":::

> [!IMPORTANT]
> Ha a **kezelés engedélyezése site Recovery lehetőséget**választja, a rendszer a tárolóban lévő összes virtuális gépre alkalmazza a beállítást.

> [!NOTE]
> Bármelyik lehetőség értesíti a frissítések kezeléséhez használt Automation-fiókról. Ha első alkalommal használja ezt a szolgáltatást egy tárolóban, a rendszer alapértelmezés szerint új Automation-fiókot hoz létre. Másik lehetőségként testreszabhatja a beállítást, és kiválaszthat egy meglévő Automation-fiókot. Minden további, a replikálás engedélyezéséhez szükséges érték az előzőleg létrehozott Automation-fiókot fogja használni. Jelenleg a legördülő menü csak azokat az Automation-fiókokat sorolja fel, amelyek ugyanabban az erőforráscsoporthoz találhatók, mint a tároló.

### <a name="manage-updates-manually"></a>Frissítések manuális kezelése

1. Ha a virtuális gépekre telepített mobilitási szolgáltatásnak új frissítései vannak, akkor a következő értesítés jelenik meg: **új site Recovery replikációs ügynök frissítése elérhető. Kattintson ide a telepítéshez.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Replikált elemek ablak":::

1. Válassza ki az értesítést a virtuális gép kiválasztási oldalának megnyitásához.
1. Válassza ki a frissíteni kívánt virtuális gépeket, majd kattintson **az OK gombra**. Minden kiválasztott virtuális gép esetében elindul a mobilitási szolgáltatás frissítése.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="Replikált elemek virtuálisgép-listája":::

## <a name="common-issues-and-troubleshooting"></a>Gyakori problémák és hibaelhárítás

Ha probléma merül fel az automatikus frissítésekkel kapcsolatban, hibaüzenet jelenik meg a **konfigurációs problémák** alatt a tároló irányítópultján.

Ha nem tudja engedélyezni az automatikus frissítéseket, tekintse meg az alábbi gyakori hibákat és ajánlott műveleteket:

- **Hiba**: nincs engedélye Azure-beli futtató fiók (egyszerű szolgáltatásnév) létrehozására, és adja meg a közreműködői szerepkört az egyszerű szolgáltatásnak.

  **Javasolt művelet**: Ellenőrizze, hogy a bejelentkezett fiók hozzá van-e rendelve közreműködőként, és próbálkozzon újra. Az engedélyek kiosztásával kapcsolatos további információkért tekintse meg a szükséges engedélyek című szakaszt, [útmutató: a portál használatával létrehozhat egy Azure ad-alkalmazást és egy egyszerű szolgáltatásnevet, amely hozzáférhet az erőforrásokhoz](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions).

  Az automatikus frissítések engedélyezése után a legtöbb probléma kijavításához válassza a **javítás**lehetőséget. Ha a javítás gomb nem érhető el, tekintse meg a bővítmény frissítési beállításai ablaktáblájában megjelenő hibaüzenetet.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Site Recovery szolgáltatás javítása gomb a bővítmény frissítési beállításainál":::

- **Hiba**: a futtató fiók nem jogosult a Recovery Services-erőforrás elérésére.

  **Javasolt művelet**: törölje, majd [hozza létre újra a futtató fiókot](/azure/automation/automation-create-runas-account). Vagy ellenőrizze, hogy az Automation futtató fiók Azure Active Directory alkalmazása hozzáférhet-e a Recovery Services-erőforráshoz.

- **Hiba**: a futtató fiók nem található. Ezek egyike törölve lett vagy nem lett létrehozva – Azure Active Directory alkalmazás, szolgáltatásnév, szerepkör, Automation-tanúsítvány eszköze, Automation-kapcsolati eszköz –, vagy az ujjlenyomat nem azonos a tanúsítvány és a kapcsolat között.

  **Javasolt művelet**: törölje, majd [hozza létre újra a futtató fiókot](/azure/automation/automation-create-runas-account).

- **Hiba**: az Automation-fiók által használt Azure-beli futtató tanúsítvány hamarosan lejár.

  A futtató fiókhoz létrehozott önaláírt tanúsítvány a létrehozás dátumától számítva egy évig lejár. A tanúsítványt bármikor meg lehet újítani a lejárata előtt. Ha regisztrált az e-mailes értesítésekre, akkor is megkapja az e-maileket, amikor egy műveletre van szükség az Ön oldaláról. Ez a hiba két hónappal a lejárati dátum előtt jelenik meg, és kritikus hibára vált, ha a tanúsítvány lejárt. Ha a tanúsítvány lejárt, az automatikus frissítés addig nem fog működni, amíg meg nem újítja.

  **Javasolt művelet**: a probléma megoldásához válassza a **javítás** , majd a **tanúsítvány megújítása**lehetőséget.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="tanúsítvány megújítása":::

  > [!NOTE]
  > A tanúsítvány megújítása után frissítse a lapot az aktuális állapot megjelenítéséhez.
