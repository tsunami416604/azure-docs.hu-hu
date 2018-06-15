---
title: Virtuális gép bejövő és kimenő forgalmának naplózása – oktatóanyag – Azure Portal | Microsoft Docs
description: Megismerheti, hogyan naplózhatja egy virtuális gép bejövő és kimenő hálózati forgalmát a Network Watcher NSG-folyamatnaplózási funkciójának használatával.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to log the network traffic to and from a VM so I can analyze it for anomalies.
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: f010bebcf1130b3061c60987ffbd4e706a030773
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32776550"
---
# <a name="tutorial-log-network-traffic-to-and-from-a-virtual-machine-using-the-azure-portal"></a>Oktatóanyag: Virtuális gép bejövő és kimenő hálózati forgalmának naplózása az Azure Portal használatával

A hálózati biztonsági csoportok (NSG-k) lehetővé teszik a virtuális gépek bejövő és kimenő forgalmának szűrését. A Network Watcher NSG-folyamatnaplózási funkciójával naplózhatja az egyes hálózati biztonsági csoportokon áthaladó hálózati forgalmat. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hálózati biztonsági csoporttal rendelkező virtuális gép létrehozása
> * A Network Watcher engedélyezése és a Microsoft.Insights szolgáltató regisztrálása
> * Forgalom naplózásának engedélyezése egy hálózati biztonsági csoport esetében a Network Watcher NSG-folyamatnaplózási funkciójának használatával
> * Naplózott adatok letöltése
> * Naplózott adatok megtekintése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Az Azure Portal bal felső sarkában kattintson az **+ Erőforrás létrehozása** gombra.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** vagy az **Ubuntu Server 17.10 VM** elemet.
3. Adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza az **OK** elemet:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVM|
    |Felhasználónév| Adjon meg egy tetszőleges felhasználónevet.|
    |Jelszó| Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport| Válassza az **Új létrehozása** elemet, és adja meg a **myResourceGroup** nevet.|
    |Hely| Válassza az **USA keleti régiója** lehetőséget.|

4. Válassza ki a virtuális gép méretét, majd kattintson a **Kiválasztás** gombra.
5. A **Beállítások** területen fogadja el az összes alapértelmezett beállítást, majd kattintson az **OK** gombra.
6. Az **Összegzés** lap **Létrehozás** területén kattintson a **Létrehozás** elemre a virtuális gép üzembe helyezésének megkezdéséhez. A virtuális gép üzembe helyezése néhány percet vesz igénybe. Várjon, amíg a virtuális gép üzembe helyezése befejeződik, mielőtt folytatná a további lépésekkel.

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne ugorjon a további lépésekre, amíg a virtuális gép létrehozása be nem fejeződött. Miközben a portál létrehozza a virtuális gépet, egy hálózati biztonsági csoportot is létrehoz **myVm-nsg** néven, és társítja azt a virtuális gép hálózati adapteréhez.

## <a name="enable-network-watcher"></a>A Network Watcher engedélyezése

Ha már engedélyezve van a Network Watcher az USA keleti régiójában, folytassa az [Insights-szolgáltató regisztrálása](#register-insights-provider) szakasszal.

1. A portálon válassza a **Minden szolgáltatás** lehetőséget. A **Szűrő** mezőbe írja be a *Network Watcher* kifejezést. Amikor a **Network Watcher** elem megjelenik az eredmények között, válassza ki.
2. Válassza a **Régiók** elemet a kibontásához, majd válassza az **USA keleti régiója** elem jobb oldalán található **...** lehetőséget az alábbi ábrán látható módon:

    ![A Network Watcher engedélyezése](./media/network-watcher-nsg-flow-logging-portal/enable-network-watcher.png)

3. Válassza a **Network Watcher engedélyezése** lehetőséget.

## <a name="register-insights-provider"></a>Insights-szolgáltató regisztrálása

Az NSG-folyamatnaplózáshoz a **Microsoft.insights** szolgáltató szükséges. A szolgáltató regisztrálásához hajtsa végre a következő lépéseket:

1. Válassza a portál bal felső sarkában található **Minden szolgáltatás** lehetőséget. A Szűrő mezőbe írja be az *Előfizetések* kifejezést. Amikor az **Előfizetések** elem megjelenik a keresési eredmények között, válassza ki.
2. Az előfizetések listájából válassza ki azt, amelyik esetében engedélyezni szeretné a szolgáltatót.
3. A **BEÁLLÍTÁSOK** területen válassza az **Erőforrás-szolgáltatók** lehetőséget.
4. Ellenőrizze, hogy a **microsoft.insighs** szolgáltató **ÁLLAPOTA** **Regisztrálva**, ahogyan az az alábbi képen is látható. Ha az állapot **Nem regisztrált**, válassza a szolgáltatótól jobbra található **Regisztrálás** lehetőséget.

    ![Szolgáltató regisztrálása](./media/network-watcher-nsg-flow-logging-portal/register-provider.png)

## <a name="enable-nsg-flow-log"></a>NSG-folyamatnapló engedélyezése

1. A rendszer az NSG-folyamatnapló adatait egy Azure Storage-fiókba fogja írni. Azure Storage-fiók létrehozásához válassza a portál bal felső sarkában található **+ Erőforrás létrehozása** elemet.
2. Válassza a **Storage** elemet, majd a **Tárfiók – blob, fájl, tábla, üzenetsor** lehetőséget.
3. Adja meg vagy válassza ki a következő adatokat, fogadja el a fennmaradó alapértelmezett adatokat, majd válassza a **Létrehozás** elemet.

    | Beállítás        | Érték                                                        |
    | ---            | ---   |
    | Name (Név)           | A hossza 3–24 karakter lehet, kizárólag kisbetűket és számokat tartalmazhat, és egyedinek kell lennie az összes Azure Storage-fiókban.                                                               |
    | Hely       | Válassza az **USA keleti régiója** lehetőséget.                                           |
    | Erőforráscsoport | Válassza a **Meglévő használata**, majd a **myResourceGroup** lehetőséget. |

    A Storage-fiók létrehozása nagyjából egy percet vesz igénybe. Ne folytassa a további lépésekkel, amíg a tárfiók létrehozása be nem fejeződött. Ha meglévő Storage-fiókot használ új létrehozása helyett, győződjön meg arról, hogy olyan fiókot választ, amely esetében a **BEÁLLÍTÁSOK** területen a **Tűzfalak és virtuális hálózatok** beállítása **Minden hálózat** (alapértelmezett).
4. Válassza a portál bal felső sarkában található **Minden szolgáltatás** lehetőséget. A **Szűrő** mezőbe írja be a *Network Watcher* kifejezést. Amikor a **Network Watcher** elem megjelenik a keresési eredmények között, válassza ki.
5. A **NAPLÓK** területen válassza az **NSG-folyamatnaplók** lehetőséget, ahogyan az a következő képen látható:

    ![NSG-k](./media/network-watcher-nsg-flow-logging-portal/nsgs.png)

6. A hálózati biztonsági csoportok közül válassza a **myVm-nsg** nevű csoportot.
7. A **Folyamatnaplók beállításai** területen válassza a **Be** lehetőséget.
8. Válassza ki a 3. lépésben létrehozott Storage-fiókot.
9. A **Megőrzés (nap)** beállítást állítsa 5 értékre, majd válassza a **Mentés** lehetőséget.

## <a name="download-flow-log"></a>Folyamatnapló letöltése

1. A portálon, a Network Watcherben válassza az **NSG-folyamatnaplók** lehetőséget a **NAPLÓK** területen.
2. Válassza **A folyamatnaplókat a konfigurált tárfiókokból töltheti le** lehetőséget, ahogyan az az alábbi képen is látható:

  ![Folyamatnaplók letöltése](./media/network-watcher-nsg-flow-logging-portal/download-flow-logs.png)

3. Válassza ki az [NSG-folyamatnapló engedélyezése](#enable-nsg-flow-log) szakasz 2. lépésében konfigurált Storage-fiókot.
4. Válassza a **Tárolók** lehetőséget a **BLOB SZOLGÁLTATÁS** területen, majd válassza az **insights-logs-networksecuritygroupflowevent** tárolót, ahogyan az az alábbi képen is látható:

    ![Tároló kiválasztása](./media/network-watcher-nsg-flow-logging-portal/select-container.png)
5. Navigáljon a mappahierarchiában a PT1H.json fájlhoz a következő képen látható módon:

    ![Naplófájl](./media/network-watcher-nsg-flow-logging-portal/log-file.png)

    A rendszer a naplófájlokat a következő elnevezési konvenció szerinti mappahierarchiába írja: https://{tárfióknév}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{előfizetés-azonosító}/RESOURCEGROUPS/{erőforráscsoport-név}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{NSGneve}/y={év}/m={hónap}/d={nap}/h={óra}/m=00/macAddress={MAC-cím}/PT1H.json

6. Válassza a PT1H.json fájl jobb oldalán található **...** elemet, majd a **Letöltés** lehetőséget.

## <a name="view-flow-log"></a>Folyamatnapló megtekintése

Az alábbi json-fájl egy példa arra, mi látható majd a PT1H.json fájlban az egyes folyamatok esetén, amelyekhez adatokat naplóz a rendszer:

```json
{
    "time": "2018-05-01T15:00:02.1713710Z",
    "systemId": "<Id>",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/<Id>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MYVM-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 1,
        "flows": [{
            "rule": "UserRule_default-allow-rdp",
            "flows": [{
                "mac": "000D3A170C69",
                "flowTuples": ["1525186745,192.168.1.4,10.0.0.4,55960,3389,T,I,A"]
            }]
        }]
    }
}
```

A **mac** érték az előző kimenetben azon hálózati adapter MAC-címét jelöli, amely a virtuális gép létrehozásakor lett létrehozva. A **flowTuples** vesszővel tagolt adatai a következők:

| Példaadatok | Az adatok jelentése   | Magyarázat                                                                              |
| ---          | ---                    | ---                                                                                      |
| 1525186745   | Időbélyeg             | Az az időpont, amikor a forgalom jelentkezett, UNIX EPOCH formátumban. Az előzőben példában látható dátum átalakítva: 2018. május 1. 14:59:05 GMT.                                                                                    |
| 192.168.1.4  | Forrás IP-címe      | A forrás IP-cím, ahonnan a forgalom érkezett.
| 10.0.0.4     | Cél IP-cím | A cél IP-cím, ahová a forgalom tartott. A 10.0.0.4 a [Virtuális gép létrehozása](#create-a-vm) szakaszban létrehozott virtuális gép magánhálózati IP-címe.                                                                                 |
| 55960        | Forrásport            | A forrásport, ahonnan a forgalom érkezett.                                           |
| 3389         | Célport       | A célport, ahová a forgalom tartott. Mivel a forgalom a 3389-es porthoz tartott, a naplófájl **UserRule_default-allow-rdp** nevű szabálya dolgozta fel.                                                |
| T            | Protokoll               | Azt jelöli, hogy a forgalom protokollja TCP (T) vagy UDP (U) volt-e.                                  |
| I            | Irány              | Azt jelöli, hogy a forgalom bejövő (I) vagy kimenő (O) volt-e.                                     |
| A            | Műveletek                 | Azt jelöli, hogy a forgalom engedélyezve (A) vagy elutasítva (D) lett-e.                                           |

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan engedélyezhető az NSG-folyamatnaplózás egy hálózati biztonsági csoport esetében. Azt is megismerhette, hogyan töltheti le és tekintheti meg a fájlokban naplózott adatokat. A json-fájlban található nyers adatok értelmezése kihívást jelenthet. Az adatok megjelenítéséhez használhatja a Network Watcher [Traffic Analytics](traffic-analytics.md) eszközét, a Microsoft [Power BI-t](network-watcher-visualize-nsg-flow-logs-power-bi.md) szolgáltatását vagy egyéb eszközöket.