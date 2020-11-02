---
title: Az Azure foglalási javaslatokkal kapcsolatos problémák elhárítása
description: Ez a cikk segítséget nyújt az Azure Portalon látható Azure foglalási javaslatokkal kapcsolatos problémák megértéséhez és elhárításához.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
ms.openlocfilehash: a3137d779908bf2791ca396068a8c9edf5d56739
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92492237"
---
# <a name="troubleshoot-azure-reservation-recommendations"></a>Az Azure foglalási javaslatokkal kapcsolatos problémák elhárítása

Ez a cikk segítséget nyújt az Azure Portalon látható Azure foglalási javaslatokkal kapcsolatos problémák megértéséhez és elhárításához. Előfordulhat, hogy egyáltalán nem lát javaslatokat, vagy olyan javaslatok jelennek meg, amelyek nem felelnek meg az elvárásainak. Rendelkezhet például fenntartott példánnyal egy adott virtuálisgép-konfigurációhoz kapcsolódóan, így arra számít, hogy hasonló virtuálisgép-konfigurációra vonatkozó javaslatot fog látni.

## <a name="symptoms"></a>Hibajelenségek

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/), és lépjen a **Foglalások** területre.
2. Válassza a **+ Hozzáadás** elemet, majd válasszon ki egy terméket.
3. Előfordulhat, hogy a **Javasolt** lapon nem lát javaslatokat, vagy olyan javaslatokat lát, amelyek nem felelnek meg az elvárásainak.

## <a name="cause"></a>Ok

Az ajánlott termékek listájának létrehozása a (megosztott vagy egyetlen előfizetésre kiterjedő) hatókörhöz kapcsolódó használat mértéke és a termékhez tartozó foglalás költségének összehasonlítása alapján történik. Ha a javaslati motor lehetséges megtakarítást észlel, javasolja egy termék megvásárlását. Ha azonban a motor semmilyen megtakarítási lehetőséget nem észlel, akkor nem javasol terméket.

Ha egy adott konfigurációval rendelkező erőforrást futtat, nem garantálható, hogy pénzt takaríthat meg a konfigurációhoz kapcsolódó foglalás megvásárlásával. Előfordulhat például, hogy csak elvétve használja az erőforrást. Ha ez a helyzet, akkor lehetséges, hogy a foglalás teljes költsége nem foglal magába megtakarítást a foglalás teljes időtartama során.

Azt is fontos megérteni, hogy milyen hatással van a kiválasztott hatókör a javaslatokra. Ha a hatókör **megosztott** , akkor a listában szereplő javaslatok olyan fenntartott példányokra vonatkoznak, amelyek esetében az Azure megtakarítást észlel a számlázási előfizetéshez társított teljes regisztrációt figyelembe véve. Ha a hatókör **egyetlen előfizetésre terjed ki** , akkor a listában szereplő javaslatok csak az előfizetésben futó erőforrásokra vonatkoznak. Lehetséges, hogy egyes virtuálisgép-méreteket a rendszer javasol az egyik hatókörhöz kapcsolódóan, a másikhoz azonban nem. Előfordulhat például, hogy a teljes regisztrációt tekintve a **Standard_B1ls** összesített használata elég magas ahhoz, hogy megérje foglalást vásárolni a regisztráció hatókörében. Viszont lehet, hogy a regisztráció egy adott előfizetése esetében nincs elegendő használat ahhoz, hogy érdemes legyen foglalást vásárolni a hatókörben. Ha vált a **megosztott** és **egyetlen előfizetésre kiterjedő** hatókör között, az különböző javaslatokat eredményezhet.

A meghatározott megtakarítási lehetőségek alapján az Azure bizonyos időtartamokra javasolhatja foglalás vásárlását, más időtartamokra viszont nem. A hároméves időtartamok például nagyobb kedvezményt kínálnak az egyéveseknél. Az Azure nagyobb valószínűséggel talál megtakarítási lehetőségeket a hároméves időtartamokhoz kapcsolódóan, mint az egyévesek esetében.

Ha szeretné megérteni, hogy az Azure miért javasol egy adott erőforrásméretet és -mennyiséget, válassza a **&lt;Mennyiség&gt; Részletek megtekintése** lehetőséget az idővel elérhető lehetséges megtakarítások részletes vizualizációjának megtekintéséhez.

:::image type="content" source="./media/troubleshoot-reservation-recommendation/see-details-link.png" alt-text="Példa a foglalási javaslat Részletek megtekintése hivatkozására" lightbox="./media/troubleshoot-reservation-recommendation/see-details-link.png" :::

## <a name="solution"></a>Megoldás

A foglalásokat bármilyen mennyiségben, tetszőleges időtartamra megvásárolhatja. Ha egy foglalást a javaslattól eltérő mennyiségben és időtartamra vásárol meg, az valószínűleg az optimálistól elmaradó megtakarítással és használattal járhat.

## <a name="next-steps"></a>Következő lépések

- A foglalási javaslatokkal kapcsolatos további információkért lásd: [Foglalásvásárlási javaslatok](determine-reservation-purchase.md).
