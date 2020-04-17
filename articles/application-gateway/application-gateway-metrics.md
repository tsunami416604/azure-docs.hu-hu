---
title: Az Azure Monitor metrikák az Application Gateway
description: Ismerje meg, hogyan használhat metrikákat az alkalmazásátjáró teljesítményének figyelésére
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 2/5/2019
ms.author: absha
ms.openlocfilehash: ebbdda39f019f374f8e5abe951d0180c0dd453f6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457875"
---
# <a name="metrics-for-application-gateway"></a>Az Alkalmazásátjáró metrikák

Az Application Gateway közzéteszi a metrikáknak nevezett adatpontokat az [Azure Monitornak](https://docs.microsoft.com/azure/azure-monitor/overview) az Alkalmazásátjáró és a háttérpéldányok teljesítményéhez. Ezek a metrikák numerikus értékek egy rendezett idősorozat-adatok, amelyek leírják az alkalmazásátjáró egy adott időpontban néhány szempontból. Ha vannak kérelmek az Application Gateway-en keresztül, méri, és elküldi a metrikák 60 másodperces időközönként. Ha nincsenek kérelmek az Application Gateway-en keresztül, vagy egy metrika nem adatai, a metrika nem jelenti. További információ: [Azure Monitor metrikák.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Az Application Gateway V2 Termékváltozat által támogatott metrikák

### <a name="timing-metrics"></a>Időzítési mutatók

Az Application Gateway számos beépített időzítési metrikát biztosít a kérelemhez és a válaszhoz kapcsolódóan, amelyek mindegyike ezredmásodpercben van mérhető. 

![](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> Ha egynél több figyelő van az Application Gateway-ben, majd mindig *szűrje a Figyelő* dimenzió, miközben összehasonlítja a különböző késési metrikák annak érdekében, hogy értelmes következtetéseket kapjon.

- **Háttérkapcsolati idő**

  A háttéralkalmazással való kapcsolat létesítésével töltött idő. 

  Ez magában foglalja a hálózati késést, valamint a háttérkiszolgáló TCP-vermének új kapcsolatok létrehozásához szükséges időt. TLS esetén a kézfogásra fordított időt is tartalmazza. 

- **Háttér–első bájt válaszideje**

  A háttérkiszolgálóval létesítés és a válaszfejléc első bájtjának fogadása között eltelt idő. 

  Ez megközelíti a *háttérrendszer-csatlakozási idő,* az alkalmazásátjáróból a háttérrendszer eléréséhez szükséges kérelem által igénybe vett időt, a háttéralkalmazás által a tartalom létrehozásához szükséges időt, az adatbázis-lekérdezések potenciális lehívását, valamint azt az időt, amelyet a válasz első bájtja az Alkalmazásátjáró háttérrendszerből való eléréséhez szükséges.

- **Háttér-hátrabájtos válaszideje**

  A háttérkiszolgálóval létesítés és a választörzs utolsó bájtjának fogadása között eltelt idő. 

  Ez megközelíti a *háttérrendszer első bájtválasz-idejének* és az adatátviteli időnek az összegét (ez a szám nagymértékben eltérhet a kért objektumok méretétől és a kiszolgálóhálózat késésétől).

- **Alkalmazásátjáró teljes ideje**

  A kérelem fogadásához, feldolgozásához és válaszának elküldéséhez szükséges átlagos idő. 

  Ez az az időköz, amely attól az időponttól kezdve, amikor az Application Gateway megkapja a HTTP-kérelem első bájtját, addig az időpontig, amikor az utolsó válaszbájtot elküldték az ügyfélnek. Ez magában foglalja az Application Gateway által igénybe vett feldolgozási időt, a *háttérrendszer utolsó bájtos válaszidejét,* az Application Gateway által az összes válasz küldéséhez szükséges időt és az *ügyfél RTT-jét.*

- **Ügyfél RTT**

  Az ügyfelek és az Application Gateway közötti átlagos oda-vissza út.



Ezek a metrikák segítségével meghatározható, hogy a megfigyelt lassulás az ügyfélhálózat, az Application Gateway teljesítménye, a háttérhálózat és a háttérkiszolgáló TCP-veremtelítettségének, a háttéralkalmazás teljesítményének vagy a nagy fájlméretnek köszönhető-e.

Például ha van egy kiugró *háttér-első bájt válaszidő-trend,* de a *háttérbeli csatlakozási idő* trend stabil, akkor arra lehet következtetni, hogy az alkalmazás átjáró háttér-késés és a kapcsolat létrehozásához szükséges idő stabil, és a kiugrás okozza a háttérbeli alkalmazás válaszidőének növekedése miatt. Másrészt, ha a *háttérrendszer első bájtválasz-idő* kiugrása a *háttérkapcsolati idő*megfelelő kiugrásához van társítva, akkor arra lehet következtetni, hogy az Application Gateway és a háttérkiszolgáló, vagy a háttérkiszolgáló TCP-veremje telített. 

Ha azt veszi észre, hogy a *háttérrendszer utolsó bájtválasz-ideje,* de a *háttérrendszer első bájt válaszideje* stabil, akkor arra lehet következtetni, hogy a kiugró pont egy nagyobb fájl kért.

Hasonlóképpen, ha az *alkalmazásátjáró teljes idő* egy tüske, de a *háttérrendszer utolsó bájt válaszideje* stabil, akkor lehet jele a teljesítmény szűk keresztmetszetet az Application Gateway, vagy egy szűk a hálózat között az ügyfél és az Application Gateway. Emellett ha az *ügyfél RTT* is rendelkezik egy megfelelő csúcs, majd azt jelzi, hogy a lebomlás miatt az ügyfél és az Application Gateway közötti hálózat.

### <a name="application-gateway-metrics"></a>Alkalmazásátjáró-metrikák

Az Application Gateway esetében a következő mérőszámok érhetők el:

- **Fogadott bájtok**

   Az alkalmazásátjáró által az ügyfelektől fogadott bájtok száma

- **Küldött bájtok**

   Az alkalmazásátjáró által az ügyfeleknek küldött bájtok száma

- **Ügyfél TLS protokoll**

   Az alkalmazásátjáróval kapcsolatot létesítő ügyfél által kezdeményezett TLS- és nem TLS-kérelmek száma. A TLS protokoll eloszlásának megtekintéséhez szűrje a TLS protokoll dimenziója szerint.

- **Jelenlegi kapacitásegységek**

   A forgalom terheléselosztásához felhasznált kapacitásegységek száma. A kapacitásegységnek három meghatározó tényezői vannak : számítási egység, állandó kapcsolatok és átviteli képesség. Minden kapacitásegység legbénult: 1 számítási egységből vagy 2500 állandó kapcsolatból vagy 2,22 Mb/s átviteli sebességből áll.

- **Aktuális számítási egységek**

   Felhasznált processzorkapacitás száma. A számítási egységet befolyásoló tényezők a TLS-kapcsolatok/mp, az URL-újraírási számítások és a WAF-szabályok feldolgozása. 

- **Jelenlegi kapcsolatok**

   Az ügyfelek és az alkalmazásátjáró között aktív egyidejű kapcsolatok teljes száma
   
- **Becsült számlázott kapacitásegységek**

  A v2 Termékváltozat, a díjszabási modell a fogyasztás által vezérelt. A kapacitásegységek a rögzített költségen felül felszámított fogyasztásalapú költséget mérik. *A becsült számlázott kapacitásegységek* azt a kapacitásegységszámot jelzik, amelyalapján a számlázás tanusítható. Ezt a program a *jelenlegi kapacitásegységek* (a forgalom terhelési egyensúlyához szükséges kapacitásegységek) és a *Rögzített számlázható kapacitásegységek* (minimális kiosztott kapacitásegységek) közötti nagyobb értékként számítja ki.

- **Sikertelen kérelmek**

  Azon kérések száma, amelyeket az Application Gateway 5xx kiszolgálóhibakódokkal kézbesített. Ez magában foglalja az Application Gateway-ből létrehozott 5xx kódokat, valamint a háttérrendszerből generált 5xx kódokat. A kérelmek száma tovább szűrhető az egyes/specifikus háttérkészlet-http beállítási kombinációnkénti szám megjelenítéséhez.
   
- **Rögzített számlázható kapacitásegységek**

  Az Application Gateway konfigurációjában a *Minimális méretezési egységek* beállítás (egy példány 10 kapacitásegységre fordítása) minimálisan kiépített kapacitásegységek minimális száma.
   
 - **Új kapcsolatok másodpercenként**

   Az ügyfelektől az Application Gateway-ig és az Application Gateway-től a háttértagokig másodpercenként létrehozott új TCP-kapcsolatok átlagos száma.


- **Válasz állapota**

   Az Application Gateway által visszaadott HTTP-válaszállapot. A válasz állapotkód-eloszlás tovább kategorizálható, hogy a válaszokat 2xx, 3xx, 4xx és 5xx kategóriákban mutassa.

- **Teljesítmény**

   Az Application Gateway által kiszolgált bájtok száma másodpercenként

- **Összes kérelem**

   Az Application Gateway által kézbesített sikeres kérelmek száma. A kérelmek száma tovább szűrhető az egyes/specifikus háttérkészlet-http beállítási kombinációnkénti szám megjelenítéséhez.

### <a name="backend-metrics"></a>Háttér-metrikák

Az Application Gateway esetében a következő mérőszámok érhetők el:

- **Háttérválaszok állapota**

  A háttérrendszerek által visszaadott HTTP-válasz állapotkódok száma. Ez nem tartalmazza az Application Gateway által létrehozott válaszkódokat. A válasz állapotkód-eloszlás tovább kategorizálható, hogy a válaszokat 2xx, 3xx, 4xx és 5xx kategóriákban mutassa.

- **Kifogástalan állapotú gazdagépek száma**

  Az állapotminta által kifogástalannak megfelelően meghatározott háttérrendszerek száma. Egy háttérkészlet alapon szűrhet egy adott háttérkészletben lévő kifogástalan állapotú gazdagépek számának megjelenítéséhez.

- **Nem kifogástalan állapotú gazdagépek száma**

  Az állapotminta által nem megfelelőállapotúnak megfelelő háttérrendszerek száma. Egy háttérkészlet alapon szűrhet egy adott háttérkészlet ben lévő nem megfelelő állapotú gazdagépek számának megjelenítéséhez.
  
- **Kérelmek percenként és egészséges gazdagépenként**

  Az egyes kifogástalan állapotú tagok által egy háttérkészletben egy perc alatt fogadott kérelmek átlagos száma. A háttérkészletet a *BackendPool HttpSettings* dimenzióval kell megadnia.  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Az Application Gateway V1 Termékváltozat által támogatott metrikák

### <a name="application-gateway-metrics"></a>Alkalmazásátjáró-metrikák

Az Application Gateway esetében a következő mérőszámok érhetők el:

- **PROCESSZOR-kihasználtság**

  Az alkalmazásátjáróhoz rendelt processzorok kihasználtságát jeleníti meg.  Normál körülmények között a processzorhasználat nem haladhatja meg rendszeresen a 90%-ot, mivel ez késleltetést okozhat az Application Gateway mögött üzemeltetett webhelyeken, és megzavarhatja az ügyfélélményt. Közvetve szabályozhatja vagy javíthatja a CPU-használatot az Application Gateway konfigurációjának módosításával a példányszám növelésével, vagy nagyobb termékváltozatméretre való áthelyezéssel, vagy mindkettő vel.

- **Jelenlegi kapcsolatok**

  Az Application Gateway-rel létrehozott aktuális kapcsolatok száma

- **Sikertelen kérelmek**

  Azon kérések száma, amelyeket az Application Gateway 5xx kiszolgálóhibakódokkal kézbesített. Ez magában foglalja az Application Gateway-ből létrehozott 5xx kódokat, valamint a háttérrendszerből generált 5xx kódokat. A kérelmek száma tovább szűrhető az egyes/specifikus háttérkészlet-http beállítási kombinációnkénti szám megjelenítéséhez.

- **Válasz állapota**

  Az Application Gateway által visszaadott HTTP-válaszállapot. A válasz állapotkód-eloszlás tovább kategorizálható, hogy a válaszokat 2xx, 3xx, 4xx és 5xx kategóriákban mutassa.

- **Teljesítmény**

  Az Application Gateway által kiszolgált bájtok száma másodpercenként

- **Összes kérelem**

  Az Application Gateway által kézbesített sikeres kérelmek száma. A kérelmek száma tovább szűrhető az egyes/specifikus háttérkészlet-http beállítási kombinációnkénti szám megjelenítéséhez.

- **A webalkalmazás tűzfalának blokkolt kérései száma**
- **Webalkalmazás-tűzfal blokkolt kérelmek elosztása**
- **Webalkalmazás-tűzfal összes szabályának elosztása**

### <a name="backend-metrics"></a>Háttér-metrikák

Az Application Gateway esetében a következő mérőszámok érhetők el:

- **Kifogástalan állapotú gazdagépek száma**

  Az állapotminta által kifogástalannak megfelelően meghatározott háttérrendszerek száma. Egy háttérkészlet alapon szűrhet egy adott háttérkészletben lévő kifogástalan állapotú gazdagépek számának megjelenítéséhez.

- **Nem kifogástalan állapotú gazdagépek száma**

  Az állapotminta által nem megfelelőállapotúnak megfelelő háttérrendszerek száma. Egy háttérkészlet alapon szűrhet egy adott háttérkészlet ben lévő nem megfelelő állapotú gazdagépek számának megjelenítéséhez.

## <a name="metrics-visualization"></a>Metrikák megjelenítése

Tallózással keresse meg az alkalmazásátjárót, a **Figyelés** csoportban válassza **a Metrikák lehetőséget.** Az elérhető értékeket a **METRIKÁK** legördülő listában találja.

Az alábbi képen egy példa látható, amelyen három mérőszám jelenik meg az elmúlt 30 percben:

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

A metrikák aktuális listájának megtekintéséhez olvassa el a [Támogatott metrikák az Azure Monitorhasználatával című témakört.](../azure-monitor/platform/metrics-supported.md)

### <a name="alert-rules-on-metrics"></a>A mérőszámokra vonatkozó riasztási szabályok

Riasztási szabályok at egy erőforrás metrikák alapján indíthatja el. Például egy riasztás hívhat ja egy webhook vagy e-mailben egy rendszergazda, ha az átviteli az alkalmazás átjáró felett, alatt, vagy egy küszöbértéket egy adott időszakban.

A következő példa végigvezeti egy riasztási szabály létrehozásán, amely e-mailt küld a rendszergazdának, miután az átviteli érték megszegi a küszöbértéket:

1. A Szabály **hozzáadása** lap megnyitásához válassza a **Metrika-riasztás hozzáadása** lehetőséget. Ezt az oldalt a mérőszámok oldalról is elérheted.

   !["Metrikariasztás hozzáadása" gomb][6]

2. A **Szabály hozzáadása** lapon töltse ki a nevet, a feltételt és a szakaszokértesítését, majd kattintson **az OK gombra.**

   * A **Feltétel választóban** válasszon egyet a négy érték közül: **Nagyobb**, **Nagyobb vagy egyenlő**, **Kisebb, mint**, vagy **Kisebb, mint .**

   * Az **Időszak** választóban válasszon ki egy öt perctől hat óráig terjedő időszakot.

   * Ha az **E-mail-tulajdonosok, közreműködők és olvasók lehetőséget választja,** az e-mail dinamikus lehet az adott erőforráshoz hozzáféréssel rendelkező felhasználók alapján. Ellenkező esetben a **További rendszergazdai e-mailek** mezőben megadhat egy vesszővel tagolt listát a felhasználókról.

   ![Szabály hozzáadása lap][7]

Ha a küszöbértéket megsértik, az alábbi képen láthatóhoz hasonló e-mail érkezik:

![E-mail a küszöbérték megszegése miatt][8]

A metrikariasztás létrehozása után megjelenik a riasztások listája. Áttekintést nyújt az összes riasztási szabályok.

![A figyelmeztetések és szabályok listája][9]

Ha többet szeretne megtudni a riasztási értesítésekről, olvassa el [a Riasztási értesítések fogadása (Figyelmeztetési értesítések fogadása) (Figyelmeztetési értesítések fogadása) témakört.](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

Ha többet szeretne megtudni a webhookokról és arról, hogyan használhatja őket a riasztásokkal, látogasson el [a Webhook konfigurálása egy Azure-metrikariasztáson című webhelyre.](../azure-monitor/platform/alerts-webhooks.md)

## <a name="next-steps"></a>További lépések

* Az [Azure Monitor naplóinak](../azure-monitor/insights/azure-networking-analytics.md)használatával vizualizálhatja a számláló- és eseménynaplókat.
* [Vizualizálja azure-tevékenységnaplóját a Power BI blogbejegyzésével.](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx)
* [Megtekintheti és elemezheti az Azure-tevékenységnaplókat a Power BI-ban és egy több](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogbejegyzésben.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
