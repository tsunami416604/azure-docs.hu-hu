---
title: Riasztások és értesítések Azure Monitor hibaelhárítása
description: Azure Monitor riasztásokkal és lehetséges megoldásokkal kapcsolatos gyakori problémák.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: beb47f961c6f24453bd49aa5807c9d801fc199a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132326"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Azure Monitor riasztásokkal kapcsolatos problémák elhárítása 

Ez a cikk a Azure Monitor riasztásokkal kapcsolatos gyakori problémákat ismerteti.

Azure Monitor riasztások proaktívan értesítik Önt, ha fontos feltételek találhatók a megfigyelési adataiban. Lehetővé teszik a problémák azonosítását és megcímzését, mielőtt a felhasználók a rendszerértesítéseket. A riasztással kapcsolatos további információkért lásd: [a Microsoft Azure riasztások áttekintése](alerts-overview.md).

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>A riasztáson elvégzett művelet vagy értesítés nem a várt módon működik

Ha egy kilőtt riasztás jelenik meg a Azure Portalban, de a műveleteivel vagy értesítéseivel kapcsolatban probléma merül fel, tekintse meg a következő részeket.

## <a name="did-not-receive-expected-email"></a>Nem érkezett meg a várt e-mail

Ha egy kilőtt riasztás jelenik meg a Azure Portalban, de nem kapta meg a rajta konfigurált e-mailt, kövesse az alábbi lépéseket: 

1. **Egy [műveleti szabály](alerts-action-rules.md)letiltotta az e-mailt**? 

    Ellenőrizze, hogy a kilőtt riasztásra kattint-e a portálon, és tekintse meg a letiltott [műveleti csoportok](action-groups.md)előzményei lapot: 

    ![Riasztási műveleti szabály elnyomásának előzményei](media/alerts-troubleshoot/history-action-rule.png)

1. **Az "e-mail-Azure Resource Manager szerepkör" művelet típusa?**

    Ez a művelet csak az előfizetési hatókörben és a *felhasználó*típusú Azure Resource Manager szerepkör-hozzárendeléseket vizsgálja.  Győződjön meg arról, hogy a szerepkört az előfizetési szinten rendelte hozzá, nem pedig az erőforrás szintjén vagy az erőforráscsoport szintjén.

1. **Az e-mail-kiszolgáló és a postaláda külső e-maileket fogad el?**

    Ellenőrizze, hogy az e három címről érkező e-mailek nincsenek-e Letiltva:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    Gyakori, hogy a belső levelezőlisták vagy terjesztési listája letiltja az e-maileket a külső e-mail-címekről. A fenti e-mail-címeket meg kell adni.  
    A teszteléshez adjon hozzá egy normál munkahelyi e-mail-címet (nem levelezési listát) a műveleti csoporthoz, és ellenőrizze, hogy érkeznek-e az e-mailekre. 

1. **A beérkezett e-mail-szabályok vagy a levélszemét-szűrők feldolgozása megtörtént?** 

    Ellenőrizze, hogy nincsenek-e e-maileket törlő beérkezett fájlok, vagy helyezze át őket egy oldal mappájába. A beérkezett fájlok szabályai például tartalmazhatnak konkrét küldőket vagy adott szavakat a tárgyban.

    Győződjön meg arról is, hogy:
    
      - az e-mail-ügyfélprogram Levélszemét-beállításai (például Outlook, Gmail)
      - a küldő korlátozza/levélszemét beállítások/karantén beállítások az e-mail kiszolgáló (például Exchange, Office 365, G-Suite)
      - az e-mail-biztonsági berendezés beállításai, ha vannak ilyenek (például a Barracuda, a Cisco). 

1. **Véletlenül leiratkozott a műveleti csoportból?** 

    A riasztási e-mailek a műveleti csoportból való leiratkozásra mutató hivatkozást biztosítanak. A következő lépésekkel ellenőrizze, hogy véletlenül leiratkozott-e a műveleti csoportból:

    1. Nyissa meg a műveleti csoportot a portálon, és keresse meg az állapot oszlopot:

    ![műveleti csoport állapota oszlop](media/alerts-troubleshoot/action-group-status.png)

    2. A leiratkozási megerősítés megkeresése az e-mailben:

    ![leiratkozott a riasztási műveleti csoportból](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Az előfizetés újbóli megadásához használja a leiratkozási visszaigazoló e-mailben kapott hivatkozást, vagy távolítsa el az e-mail-címet a műveleti csoportból, majd adja vissza újra. 
 
1. **A minősítés korlátozott, mert számos e-mail-cím egyetlen e-mail-címre fog rendelkezni?** 

    Az e-mailek díjszabása legfeljebb 100 e-mail-címre van [korlátozva](alerts-rate-limiting.md) minden egyes e-mail-címre. Ha átadja ezt a küszöbértéket, a rendszer eldobott további értesítő e-maileket.  Ellenőrizze, hogy kapott-e egy üzenetet, amely jelzi, hogy az e-mail-címe átmenetileg korlátozott: 
 
   ![E-mailek aránya korlátozott](media/alerts-troubleshoot/email-paused.png)

   Ha nagy mennyiségű értesítést szeretne kapni a ráta korlátozása nélkül, érdemes lehet egy másik műveletet, például webhookot, Logic app-t, Azure-függvényt vagy Automation-runbookok használni, amelynek egyik mértéke sem korlátozott. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Nem érkezett meg a várt SMS-, hanghívási vagy leküldéses értesítés

Ha a portálon egy kilőtt riasztás jelenik meg, de nem kapta meg az SMS-t, a hanghívást vagy a leküldéses értesítést, amelyet korábban konfigurált, kövesse az alábbi lépéseket: 

1. **Egy [műveleti szabály](alerts-action-rules.md)letiltotta a műveletet?** 

    Ellenőrizze, hogy a kilőtt riasztásra kattint-e a portálon, és tekintse meg a letiltott [műveleti csoportok](action-groups.md)előzményei lapot: 

    ![Riasztási műveleti szabály elnyomásának előzményei](media/alerts-troubleshoot/history-action-rule.png)

   Ha ez nem szándékos, módosíthatja, letilthatja vagy törölheti a műveleti szabályt.
 
1. **SMS/Voice: helyes a telefonszáma?**

   Az SMS-művelet elírását az országkód vagy a telefonszám esetében olvassa el. 
 
1. **SMS/Voice: korlátozott a díjszabás?** 

    Az SMS-és hanghívások száma legfeljebb egy, a telefonszámon öt percenkénti értesítésre korlátozódik. Ha átadja ezt a küszöbértéket, a rendszer elveti az értesítéseket. 

      - Hanghívás – tekintse meg a hívások előzményeit, és ellenőrizze, hogy az előző öt percben az Azure-tól eltérő hívást kapott-e. 
      - SMS – tekintse meg az SMS előzményeit, amely azt jelzi, hogy a telefonszáma korlátozott. 

    Ha nagy mennyiségű értesítést szeretne kapni a ráta korlátozása nélkül, érdemes lehet egy másik műveletet, például webhookot, Logic app-t, Azure-függvényt vagy Automation-runbookok használni, amelynek egyik mértéke sem korlátozott. 
 
1. **SMS: véletlenül leiratkozott a műveleti csoportból?**

    Nyissa meg az SMS-előzményeket, és ellenőrizze, hogy kikapcsolta-e az SMS-kézbesítést az adott műveleti csoportból (a LETILTÁS action_group_short_name válasz használatával) vagy az összes műveleti csoportból (a leállítási válasz használatával). Az előfizetés ismételt előfizetéséhez küldje el a megfelelő SMS-parancsot (engedélyezze action_group_short_name vagy indítsa el), vagy távolítsa el az SMS-műveletet a műveleti csoportból, majd adja újra újra.  További információ: [SMS-riasztás viselkedése a műveleti csoportokban](alerts-sms-behavior.md).

1. **Véletlenül letiltotta az értesítéseket a telefonon?**

   A legtöbb mobiltelefon lehetővé teszi a hívások vagy SMS-ek letiltását bizonyos telefonszámokból vagy rövid kódokból, illetve leküldéses értesítések letiltására bizonyos alkalmazásokból (például a Azure mobile app). Annak ellenőrzéséhez, hogy véletlenül letiltotta-e az értesítéseket a telefonján, keresse meg a telefonos operációs rendszerre és a modellre vonatkozó dokumentációt, vagy végezzen el egy másik telefonszámot és telefonszámot. 

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>A program egy másik típusú műveletet várt, de nem 

Ha a portálon egy kilőtt riasztás látható, de a beállított művelete nem aktiválódik, kövesse az alábbi lépéseket: 

1. **Egy műveleti szabály letiltotta a műveletet?** 

    Ellenőrizze, hogy a kilőtt riasztásra kattint-e a portálon, és tekintse meg a letiltott [műveleti csoportok](action-groups.md)előzményei lapot: 

    ![Riasztási műveleti szabály elnyomásának előzményei](media/alerts-troubleshoot/history-action-rule.png)
 
    Ha ez nem szándékos, módosíthatja, letilthatja vagy törölheti a műveleti szabályt. 

1. **Nem aktiválódik a webhook?**

    1. **Le lettek tiltva a forrás IP-címei?**
    
       A webhook által meghívott [IP-címek](action-groups.md#action-specific-information) engedélyezési listája.

    1. **A webhook-végpont megfelelően működik?**

       Ellenőrizze, hogy helyes-e a konfigurált webhook-végpont, és a végpont megfelelően működik-e. Tekintse meg a webhook-naplókat, vagy adja meg a kódját, hogy megvizsgálható legyen (például naplózza a bejövő adattartalmat). 

    1. **A Slack vagy a Microsoft Teams szolgáltatást hívja?**  
    Ezek a végpontok egy adott JSON-formátumot várnak. A Logic app-műveletek konfigurálásához kövesse az [alábbi utasításokat](action-groups-logic-app.md) .

    1. **A webhook nem válaszol, vagy hibát adott vissza?** 

        A webhook válaszának időtúllépési időtartama 10 másodperc. A webhook-hívást a rendszer a következő HTTP-állapotkódok visszaadásakor két további alkalommal próbálkozik: 408, 429, 503, 504, vagy ha a HTTP-végpont nem válaszol. Az első újrapróbálkozás 10 másodperc után történik. A második és a végső újrapróbálkozás 100 másodperc után történik. Ha a második újrapróbálkozás sikertelen, a rendszer 30 percig semelyik műveletcsoport számára nem hívja meg újra a végpontot.

## <a name="action-or-notification-happened-more-than-once"></a>A művelet vagy az értesítés többször is történt 

Ha többször is kapott értesítést egy riasztásról (például egy e-mailről vagy SMS-ről), vagy a riasztás műveletét (például a webhookot vagy az Azure-függvényt) többször is elindították, kövesse az alábbi lépéseket: 

1. **Valóban ugyanez a riasztás?** 

    Bizonyos esetekben több hasonló riasztást is kilőttek egy időben. Így előfordulhat, hogy úgy tűnik, mintha ugyanaz a riasztás többször is kiváltotta a műveleteit. A műveletnapló riasztási szabálya például beállítható úgy, hogy az esemény megkezdése esetén mindkettőt elindítsa, és ha befejeződött (sikeres vagy sikertelen), az Event Status (esemény állapota) mezőben nem végez szűrést. 

    Annak ellenőrzése érdekében, hogy ezek a műveletek vagy értesítések különböző riasztásokból származnak-e, vizsgálja meg a riasztás részleteit, például az időbélyegét, vagy a riasztás azonosítóját vagy a korrelációs azonosítóját. Azt is megteheti, hogy a portálon megtalálhatja a felgyújtott riasztások listáját. Ebben az esetben módosítania kell a riasztási szabály logikáját, vagy más módon konfigurálnia kell a riasztás forrását. 

1. **Ismétlődik a művelet több műveleti csoportban?** 

    A riasztások elindítását követően az egyes műveleti csoportokat egymástól függetlenül dolgozzák fel. Ha tehát egy művelet (például egy e-mail-cím) több aktivált műveleti csoportban jelenik meg, akkor a rendszer egy műveleti csoportonként egyszer hívja meg. 

    A műveleti csoportok aktiválásának vizsgálatához tekintse meg a riasztási előzmények lapot. A riasztási szabályban meghatározott műveleti csoportok és műveleti csoportok is megjelennek a riasztáshoz a műveleti szabályok szerint: 

    ![Több műveleti csoportban ismétlődő művelet](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>A művelet vagy az értesítés nem várt tartalmat tartalmaz

Ha megkapta a riasztást, de úgy véli, hogy egyes mezői hiányoznak vagy helytelenek, kövesse az alábbi lépéseket: 

1. **A megfelelő formátumot választotta a művelethez?** 

    Minden Művelettípus (e-mail-cím, webhook stb.) két formátumban van – az alapértelmezett, örökölt formátum és az [újabb általános séma formátuma](alerts-common-schema.md). A műveleti csoport létrehozásakor a kívánt formátumot kell megadnia. a műveleti csoportok különböző műveletei különböző formátumokkal rendelkezhetnek. 

    A webhook művelethez például: 

    ![webhook műveleti séma beállítása](media/alerts-troubleshoot/webhook.png)

    Ellenőrizze, hogy a műveleti szinten megadott formátum a várt érték-e. Előfordulhat például, hogy olyan programkódot fejlesztett ki, amely válaszol a riasztásokra (webhook, függvény, logikai alkalmazás stb.), ami egy formátumot vár, de később a műveletben, vagy egy másik személy eltérő formátumot adott meg.  

    Továbbá tekintse meg a [tevékenységek naplójának riasztásait](activity-log-alerts-webhook.md)(JSON) a [naplózott keresési riasztások](alerts-log-webhook.md) (a Application Insights és a log Analytics esetében is), a [metrikus riasztások](alerts-metric-near-real-time.md#payload-schema)esetében a [gyakori riasztási séma](alerts-common-schema-definitions.md)esetében, valamint az elavult [klasszikus metrikai riasztásokat](alerts-webhooks.md).

 
1. **Műveletnapló riasztásai: a tevékenység naplójában elérhető információk?** 

    A [műveletnapló riasztásai](activity-log-alerts.md) olyan riasztások, amelyek az Azure-beli tevékenység naplójában írt eseményeken alapulnak, például az Azure-erőforrások létrehozásával, frissítésével vagy törlésével, a szolgáltatás állapotával és erőforrás-állapotával kapcsolatos eseményekkel, valamint a Azure Advisor és Azure Policy eredményeivel kapcsolatban. Ha a műveletnapló alapján riasztást kapott, de egyes szükséges mezők hiányoznak vagy helytelenek, először tekintse meg az eseményeket a tevékenység naplójában. Ha az Azure-erőforrás nem írta meg a keresett mezőket a tevékenység naplójában, akkor ezek a mezők nem kerülnek bele a megfelelő riasztásba. 

## <a name="action-rule-is-not-working-as-expected"></a>A műveleti szabály nem a várt módon működik 

Ha a portálon egy kilőtt riasztás látható, de a kapcsolódó műveleti szabály nem a várt módon működött, kövesse az alábbi lépéseket: 

1. **Engedélyezve van a műveleti szabály?** 

    A műveleti szabály állapota oszlopban ellenőrizze, hogy a kapcsolódó műveleti szerepkör engedélyezve van-e. 

    ![grafikus](media/alerts-troubleshoot/action-rule-status.png) 

    Ha nincs engedélyezve, engedélyezheti a műveleti szabályt úgy, hogy kijelöli, majd az Engedélyezés lehetőségre kattint. 

1. **A szolgáltatás állapota riasztás?** 

    A szolgáltatás állapotával kapcsolatos riasztásokat (figyelő szolgáltatás = "Service Health") nem érinti a műveleti szabályok. 

1. **A műveleti szabály a riasztásra reagál?** 

    Ellenőrizze, hogy a műveleti szabály feldolgozta-e a riasztást úgy, hogy rákattint a kilőtt riasztásra a portálon, és megtekinti az előzmények lapot.

    Íme egy példa a műveleti szabályra az összes műveleti csoport letiltásához: 
 
     ![Riasztási műveleti szabály elnyomásának előzményei](media/alerts-troubleshoot/history-action-rule.png)

    Példa egy másik műveleti csoport hozzáadására szolgáló műveleti szabályra:

    ![Több műveleti csoportban ismétlődő művelet](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **A műveleti szabály hatóköre és a szűrő megfelel a kilőtt riasztásnak?** 

    Ha úgy gondolja, hogy a műveleti szabálynak kilőtt, de nem volt, vagy nem kellett volna, de nem tette volna meg, alaposan vizsgálja meg a műveleti szabály hatókörét és a szűrési feltételeket, valamint a kilőtt riasztás tulajdonságait. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>Kilőtt riasztás riasztási azonosítójának megkeresése

Ha egy adott kilőtt riasztásra vonatkozó esetet nyit meg (például – ha nem kapta meg az e-mail-értesítést), meg kell adnia a riasztás azonosítóját. 

A megkereséséhez kövesse az alábbi lépéseket:

1. A Azure Portal navigáljon a kilőtt riasztások listájához, és keresse meg az adott riasztást. A szűrők segítségével megkeresheti. 

1. A riasztás részleteinek megnyitásához kattintson a riasztásra. 

1. Görgessen lefelé az első lap riasztás mezőiben (az összefoglalás lapon), amíg meg nem találja, és másolja. Ebben a mezőben a "másolás a vágólapra" segítő gomb is megtalálható.  

    ![riasztás azonosítójának keresése](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Probléma a műveleti szabályok létrehozásával, frissítésével vagy törlésével kapcsolatban a Azure Portalban

Ha hiba történt a [műveleti szabály](alerts-action-rules.md)létrehozása, frissítése vagy törlése során, kövesse az alábbi lépéseket: 

1. **Hibaüzenetet kapott?**  

    Rendelkeznie kell a [figyelő közreműködői beépített szerepkörrel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor), vagy a műveleti szabályokkal és riasztásokkal kapcsolatos konkrét engedélyekkel.

1. **Ellenőrizte a műveleti szabály paramétereit?**  

    Győződjön meg a [műveleti szabály dokumentációjában](alerts-action-rules.md)vagy a [műveleti szabály PowerShell set-AzActionRule](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0) parancsának megtekintéséhez. 


## <a name="next-steps"></a>További lépések
- Ha naplózási riasztást használ, tekintse meg a [riasztások hibaelhárítása](alert-log-troubleshoot.md)című témakört is.
- Lépjen vissza a [Azure Portal](https://portal.azure.com) , és ellenőrizze, hogy megoldotta-e a problémát a fenti útmutatás alapján 
