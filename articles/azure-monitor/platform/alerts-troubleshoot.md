---
title: Az Azure Monitor riasztásainak és értesítéseinek hibaelhárítása
description: Gyakori problémák az Azure Monitor riasztásokkal és a lehetséges megoldásokkal.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: beb47f961c6f24453bd49aa5807c9d801fc199a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132326"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Az Azure Monitor riasztásaiban felmerülő problémák elhárítása 

Ez a cikk ismerteti az Azure Monitor riasztási gyakori problémák.

Az Azure Monitor-riasztások proaktív módon értesítik Önt, ha fontos feltételek találhatók a figyelési adatokban. Lehetővé teszik a problémák azonosítását és kezelését, mielőtt a rendszer felhasználói észrevennék őket. A riasztásokról a [Microsoft Azure riasztásainak áttekintése](alerts-overview.md)című témakörben olvashat bővebben.

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>A riasztáson végrehajtott művelet vagy értesítés nem a várt módon működött

Ha egy kiküldött riasztást az Azure Portalon, de van egy probléma néhány műveletvagy értesítés, tekintse meg a következő szakaszokat.

## <a name="did-not-receive-expected-email"></a>Nem kaptam meg a várt e-mailt

Ha megjelenik egy kirúgott riasztás az Azure Portalon, de nem kapta meg a róla konfigurált e-mailt, kövesse az alábbi lépéseket: 

1. **Az e-mailt letiltotta egy [műveletszabály?](alerts-action-rules.md)** 

    Ellenőrizze a portálon a kilőtt riasztásra kattintva, és tekintse meg az előzmények lapot az elnyomott [műveletcsoportokról:](action-groups.md) 

    ![Riasztási művelet szabályelnyomási előzményei](media/alerts-troubleshoot/history-action-rule.png)

1. **Az "E-mail azure-erőforrás-kezelői szerepkör" művelet típusa?**

    Ez a művelet csak az Azure Resource Manager szerepkör-hozzárendeléseit vizsgálja, amelyek az előfizetés hatókörében vannak, és *felhasználó típusúak.*  Győződjön meg arról, hogy a szerepkört az előfizetés szintjén rendelte hozzá, és nem az erőforrás vagy az erőforráscsoport szintjén.

1. **Az e-mail kiszolgáló és a postaláda elfogadja a külső e-maileket?**

    Ellenőrizze, hogy az e-mailek nem vannak-e letiltva:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    Gyakori, hogy a belső levelezési listák vagy terjesztési listák blokkolják a külső e-mail címekről érkező e-maileket. Meg kell, hogy whitelist a fenti e-mail címeket.  
    A teszteléshez adjon hozzá egy normál munkahelyi e-mail címet (nem levelezési listát) a műveletcsoporthoz, és ellenőrizze, hogy érkeznek-e értesítések az adott e-mailre. 

1. **Az e-mailt a beérkezett üzenetekre vonatkozó szabályok vagy a spamszűrő dolgozta fel?** 

    Ellenőrizze, hogy nincsenek-e olyan beérkezett üzenetekre vonatkozó szabályok, amelyek törlik ezeket az e-maileket, vagy áthelyezik őket egy mellékmappába. A beérkezett üzenetekre vonatkozó szabályok például elkaphatnak bizonyos feladókat vagy a tárgyban lévő szavakat.

    Is, ellenőrizze:
    
      - az e-mail kliens (például az Outlook, a Gmail) spam beállításai
      - a feladó korlátozza / spam beállítások / karantén beállításait az e-mail szerver (mint az Exchange, Office 365, G-suite)
      - az e-mail biztonsági készülék beállításait, ha vannak ilyenek (például Barracuda, Cisco). 

1. **Véletlenül leiratkozott az akciócsoportról?** 

    A figyelmeztető e-mailek egy hivatkozást biztosítanak a műveletcsoportból való leiratkozáshoz. Annak ellenőrzése, hogy véletlenül leiratkozott-e erről a műveletcsoportról:

    1. Nyissa meg a műveletcsoportot a portálon, és ellenőrizze az Állapot oszlopot:

    ![műveletcsoport állapotoszlopa](media/alerts-troubleshoot/action-group-status.png)

    2. Keressen e-mailben a leiratkozás megerősítésére:

    ![leiratkozott a riasztási műveletcsoportból](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Ha újra szeretne előfizetni – használja a leiratkozást megerősítő e-mailben található hivatkozást, vagy távolítsa el az e-mail címet az akciócsoportból, majd adja hozzá újra. 
 
1. **Korlátozott minősítést kapott, mivel sok e-mail érkezik egyetlen e-mail címre?** 

    Az e-mail sebessége óránként legfeljebb 100 e-mailre [korlátozódik](alerts-rate-limiting.md) minden egyes e-mail címre. Ha eléri ezt a küszöbértéket, további e-mail értesítések et eldobunk.  Ellenőrizze, hogy kapott-e olyan üzenetet, amely arról jelzi, hogy az e-mail címe ideiglenesen korlátozott: 
 
   ![E-mail arány korlátozott](media/alerts-troubleshoot/email-paused.png)

   Ha nagy mennyiségű értesítést szeretne kapni sebességkorlátozás nélkül, fontolja meg egy másik művelet, például a webhook, a logikai alkalmazás, az Azure-függvény vagy az automatizálási runbookok használatát, amelyek egyike sem korlátozott. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Nem kapott várt SMS-t, hanghívást vagy leküldéses értesítést

Ha a portálon megjelenik egy kirúgott riasztás, de nem kapta meg a beállított SMS-t, hanghívást vagy leküldéses értesítést, kövesse az alábbi lépéseket: 

1. **A műveletet [műveletszabály](alerts-action-rules.md)tiltotta el?** 

    Ellenőrizze a portálon a kilőtt riasztásra kattintva, és tekintse meg az előzmények lapot az elnyomott [műveletcsoportokról:](action-groups.md) 

    ![Riasztási művelet szabályelnyomási előzményei](media/alerts-troubleshoot/history-action-rule.png)

   Ha ez nem volt szándékos, módosíthatja, letilthatja vagy törölheti a műveletszabályt.
 
1. **SMS / hang: Helyes a telefonszáma?**

   Ellenőrizze, hogy az SMS-művelet nem tartalmaz-e elírásokat az országkódban vagy a telefonszámon. 
 
1. **SMS / hang: voltál arány korlátozott?** 

    Az SMS- és hanghívások sebessége telefonszámonként öt percenként legfeljebb egy értesítésre korlátozódik. Ha eléri ezt a küszöbértéket, az értesítések ellesznek dobva. 

      - Hanghívás – ellenőrizze a híváselőzményeket, és ellenőrizze, hogy az előző öt percben más hívás ad-e az Azure-ból. 
      - SMS - ellenőrizze az SMS-előzményeket egy üzenetben, amely azt jelzi, hogy a telefonszáma korlátozott volt. 

    Ha nagy mennyiségű értesítést szeretne kapni sebességkorlátozás nélkül, fontolja meg egy másik művelet, például a webhook, a logikai alkalmazás, az Azure-függvény vagy az automatizálási runbookok használatát, amelyek egyike sem korlátozott. 
 
1. **SMS: Véletlenül leiratkozott az akciócsoportról?**

    Nyissa meg az SMS-előzményeket, és ellenőrizze, hogy leiratkozott-e az SMS-kézbesítésről ettől az adott műveletcsoporttól (a TILTó action_group_short_name válasz használatával) vagy az összes műveletcsoportból (a STOP válasz használatával). Ha újra szeretne feliratkozni, küldje el a megfelelő SMS parancsot (ENABLE action_group_short_name vagy START), vagy távolítsa el az SMS műveletet a műveletcsoportból, majd adja hozzá újra.  További információt az [SMS-riasztások viselkedése műveletcsoportokban](alerts-sms-behavior.md)című témakörben talál.

1. **Véletlenül letiltotta az értesítéseket a telefonján?**

   A legtöbb mobiltelefon lehetővé teszi, hogy letiltsa a hívásokat vagy az SMS-eket bizonyos telefonszámokról vagy rövid kódokról, vagy letiltsa az adott alkalmazásokból (például az Azure mobilalkalmazásból származó leküldéses értesítéseket). Annak ellenőrzéséhez, hogy véletlenül blokkolta-e az értesítéseket a telefonján, keresse meg a telefon operációs rendszerének és modelljének dokumentációját, vagy tesztelje egy másik telefonszámmal és telefonszámmal. 

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>Más típusú műveletet várt, de nem 

Ha egy kiváltott riasztás látható a portálon, de a konfigurált művelet nem aktiválódott, kövesse az alábbi lépéseket: 

1. **A műveletet műveletszabály eltiakarta?** 

    Ellenőrizze a portálon a kilőtt riasztásra kattintva, és tekintse meg az előzmények lapot az elnyomott [műveletcsoportokról:](action-groups.md) 

    ![Riasztási művelet szabályelnyomási előzményei](media/alerts-troubleshoot/history-action-rule.png)
 
    Ha ez nem volt szándékos, módosíthatja, letilthatja vagy törölheti a műveletszabályt. 

1. **Nem vált ki egy webhook?**

    1. **Le tiltották a forrás IP-címeit?**
    
       Engedélyezési lista az [IP-címek,](action-groups.md#action-specific-information) amelyek a webhook hívják.

    1. **Megfelelően működik a webhook-végpont?**

       Ellenőrizze, hogy a konfigurált webhook-végpont helyes-e, és a végpont megfelelően működik-e. Ellenőrizze a webhook naplók vagy eszköz a kódot, így meg tudná vizsgálni (például naplózza a bejövő hasznos adat). 

    1. **A Slacket vagy a Microsoft Teamst hívja?**  
    Ezek a végpontok elvárja egy adott JSON formátumban. Kövesse [az alábbi utasításokat](action-groups-logic-app.md) a logikai alkalmazás művelet konfigurálásához.

    1. **A webhook nem reagált, vagy hibákat adott vissza?** 

        A webhook-válasz időbeli elállási ideje 10 másodperc. A webhook-hívás a következő HTTP-állapotkódok visszaadásakor legfeljebb két további alkalommal lesz újra próbálkozva: 408, 429, 503, 504, vagy ha a HTTP-végpont nem válaszol. Az első újrapróbálkozás 10 másodperc után történik. A második és egyben utolsó újrapróbálkozás 100 másodperc után történik. Ha a második újrapróbálkozás sikertelen, a rendszer 30 percig semelyik műveletcsoport számára nem hívja meg újra a végpontot.

## <a name="action-or-notification-happened-more-than-once"></a>A művelet vagy értesítés többször is megtörtént 

Ha egy riasztásról (például e-mailről vagy SMS-ről) többször is értesítést kapott, vagy a riasztás művelete (például webhook vagy Azure-funkció) többször is működésbe lépett, kövesse az alábbi lépéseket: 

1. **Tényleg ugyanaz a figyelmeztetés?** 

    Bizonyos esetekben több hasonló riasztás tanusított körülbelül ugyanabban az időben. Szóval, úgy tűnhet, hogy ugyanaz a riasztás többször is aktiválta a tetteit. Előfordulhat például, hogy egy tevékenységnapló-riasztási szabály úgy van beállítva, hogy az esemény indításakor, valamint az esemény állapotmezőjének szűrésének sikertelenségor (sikeres vagy sikertelen) működésbe lépve is elinduljon. 

    Annak ellenőrzéséhez, hogy ezek a műveletek vagy értesítések különböző riasztásokból származnak-e, vizsgálja meg a riasztás részleteit, például az időbélyegét és a riasztásazonosítót vagy a korrelációs azonosítóját. Másik lehetőségként ellenőrizze a kirúgott riasztások listáját a portálon. Ebben az esetben módosítania kell a riasztási szabály logikáját, vagy más módon kell konfigurálnia a riasztási forrást. 

1. **Ismétlődik a művelet több műveletcsoportban is?** 

    Riasztás esetén a rendszer egymástól függetlenül dolgozza fel a műveletcsoportokat. Így ha egy művelet (például egy e-mail cím) jelenik meg több aktivált műveletcsoportban, akkor műveletcsoportonként egyszer kell hívni. 

    A műveletcsoportok aktiválásának ellenőrzéséhez ellenőrizze a riasztási előzmények lapot. A riasztási szabályban definiált mindkét műveletcsoport, valamint a műveletszabályok által a riasztáshoz hozzáadott műveletcsoportok is megjelennek: 

    ![Művelet ismétlődik több műveletcsoportban](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>A művelet vagy értesítés nem várt tartalommal rendelkezik

Ha megkapta a riasztást, de úgy véli, hogy néhány mezője hiányzik vagy helytelen, kövesse az alábbi lépéseket: 

1. **A műveletnek megfelelő formátumot választotta?** 

    Minden művelettípus (e-mail, webhook stb.) két formátummal rendelkezik : az alapértelmezett, az örökölt formátum és az [újabb közös sémaformátum](alerts-common-schema.md). Amikor létrehoz egy műveletcsoportot, megadhatja a műveletenként kívánt formátumot – a műveletcsoportok különböző műveletei eltérő formátumúak lehetnek. 

    Például webhook művelet: 

    ![webhook műveletséma beállítás](media/alerts-troubleshoot/webhook.png)

    Ellenőrizze, hogy a művelet szintjén megadott formátum megfelel-e a vártnak. Előfordulhat például, hogy olyan kódot fejlesztett ki, amely válaszol a riasztásokra (webhook, függvény, logikai alkalmazás stb.), és egy formátumot vár, de később a műveletben Ön vagy egy másik személy más formátumot adott meg.  

    Emellett ellenőrizze a hasznos adatformátumban (JSON) a [tevékenységnapló-riasztásokat,](activity-log-alerts-webhook.md)a [naplókeresési riasztásokat](alerts-log-webhook.md) (mind az Application Insights, mind a log analytics), a [metrikariasztásokat](alerts-metric-near-real-time.md#payload-schema), a [közös riasztási sémát](alerts-common-schema-definitions.md)és az elavult [klasszikus metrikariasztásokat.](alerts-webhooks.md)

 
1. **Tevékenységnapló-riasztások: Elérhetők az adatok a tevékenységnaplóban?** 

    [A tevékenységnapló-riasztások](activity-log-alerts.md) olyan riasztások, amelyek az Azure-tevékenységnaplóba írt eseményeken alapulnak, például az Azure-erőforrások létrehozásával, frissítésével vagy törlésével kapcsolatos eseményeken, a szolgáltatás állapot- és erőforrás-állapoteseményeken, illetve az Azure Advisor és az Azure-szabályzat megállapításain. Ha a tevékenységnapló alapján kapott riasztást, de néhány szükséges mező hiányzik vagy helytelen, először ellenőrizze magát a tevékenységnaplóban lévő eseményeket. Ha az Azure-erőforrás nem írta meg a keresett mezőket a tevékenységnapló-esemény, ezek a mezők nem szerepelnek a megfelelő riasztást. 

## <a name="action-rule-is-not-working-as-expected"></a>A műveletszabály nem a várt módon működik 

Ha egy kilőtt riasztás látható a portálon, de egy kapcsolódó műveletszabály nem a várt módon működött, kövesse az alábbi lépéseket: 

1. **Engedélyezve van a műveletszabály?** 

    Ellenőrizze a műveletszabály állapotoszlopában, hogy a kapcsolódó műveletszerepkör engedélyezve van-e. 

    ![Grafikus](media/alerts-troubleshoot/action-rule-status.png) 

    Ha nincs engedélyezve, engedélyezheti a műveletszabályt, ha kijelöli, majd az Engedélyezés gombra kattint. 

1. **Ez egy szolgáltatás állapotriasztás?** 

    A szolgáltatás állapotriasztásait (figyelőszolgáltatás = "Szolgáltatás állapota") nem érintik a műveletszabályok. 

1. **A műveletszabály a riasztásra hat?** 

    Ellenőrizze, hogy a műveletszabály feldolgozta-e a riasztást a portálon az elindított riasztásra kattintva, és tekintse meg az előzmények lapot.

    Íme egy példa az összes műveletcsoportot letiltó műveletszabályra: 
 
     ![Riasztási művelet szabályelnyomási előzményei](media/alerts-troubleshoot/history-action-rule.png)

    Íme egy példa egy műveletszabályra, amely egy másik műveletcsoportot ad hozzá:

    ![Művelet ismétlődik több műveletcsoportban](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **A műveletszabály hatóköre és a szűrő megfelel az elindított riasztásnak?** 

    Ha úgy gondolja, hogy a műveletszabálynak el kellett volna lennie, de nem kellett volna, vagy hogy nem kellett volna elsülnie, de így történt, gondosan vizsgálja meg a műveletszabály hatókörét és a szűrőfeltételeket az ellett tüzelt riasztás tulajdonságaival szemben. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>A kilőtt riasztás riasztási azonosítójának megkeresése

Amikor megnyit egy esetet egy adott tüzelésű riasztás (például – ha nem kapta meg az e-mail értesítést), meg kell adnia a riasztásazonosítót. 

A hely megkereséséhez kövesse az alábbi lépéseket:

1. Az Azure Portalon keresse meg a kirúgott riasztások listáját, és keresse meg az adott riasztást. A szűrők segítségével megkeresheti azt. 

1. Kattintson a riasztásra a riasztás részleteinek megnyitásához. 

1. Görgessen le az első lap (az összegzés lap) riasztási mezőiben, amíg meg nem találja, és másolja azt. Ez a mező tartalmaz egy "Másolás vágólapra" segédgombot is, amelyet használhat.  

    ![riasztási azonosító keresése](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Probléma a műveletszabályok létrehozása, frissítése vagy törlése az Azure Portalon

Ha hiba történt egy [műveletszabály](alerts-action-rules.md)létrehozása, frissítése vagy törlése közben, kövesse az alábbi lépéseket: 

1. **Engedélyhiba történt?**  

    Rendelkeznie kell a [figyelési közreműködő beépített szerepkörrel,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)vagy a műveletszabályokhoz és riasztásokhoz kapcsolódó konkrét engedélyekkel.

1. **Ellenőrizte a műveletszabály paramétereit?**  

    Ellenőrizze a [műveletszabály dokumentációját,](alerts-action-rules.md)vagy a [PowerShell Set-AzActionRule](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0) parancsot. 


## <a name="next-steps"></a>További lépések
- Naplóriasztás használata esetén olvassa el a [Naplóriasztások hibaelhárítása](alert-log-troubleshoot.md)című témakört is.
- Lépjen vissza az [Azure Portalra,](https://portal.azure.com) és ellenőrizze, hogy a fenti útmutatással megoldotta-e a problémát. 
