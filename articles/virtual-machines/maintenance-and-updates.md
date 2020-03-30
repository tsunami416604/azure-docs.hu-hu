---
title: Karbantartás és frissítések
description: Az Azure-ban futó virtuális gépek karbantartásának és frissítéseinek áttekintése.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/18/2019
ms.author: shants
ms.openlocfilehash: eaf7616b3bd69828829342b4dca9247c009d3475
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250230"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Az Azure-ban futó virtuális gépek karbantartása

Az Azure rendszeresen frissíti a platformmegbízhatóság, a teljesítmény és a biztonság a gazdagép-infrastruktúra a virtuális gépek. E frissítések célja a szoftverösszetevők üzemeltetési környezetben történő javításától a hálózati összetevők frissítéséig, illetve a hardverleszerelésig terjed. 

A frissítések ritkán érintik a központi virtuális gépeket. Ha a frissítések nek van hatása, az Azure a legkevésbé befolyásoló módszert választja a frissítésekhez:

- Ha a frissítés nem igényel újraindítást, a virtuális gép szünetel, amíg az állomás frissül, vagy a virtuális gép élőáttelepítésegy már frissített gazdagép. 
- Ha a karbantartás újraindítást igényel, értesítést kap a tervezett karbantartásról. Az Azure egy időablakot is biztosít, amelyben saját maga is elindíthatja a karbantartást, az Ön számára működő időpontban. Az önfenntartó időszak általában 30 nap, kivéve, ha a karbantartás sürgős. Az Azure olyan technológiákba fektet be, amelyek csökkentik azon esetek számát, amelyekben a tervezett platformkarbantartás a virtuális gépek újraindítását igényli. A tervezett karbantartás kezelésével kapcsolatos tudnivalókért olvassa el a Tervezett karbantartási értesítések kezelése az Azure [CLI,](maintenance-notifications-cli.md)a [PowerShell](maintenance-notifications-powershell.md) vagy a portál használatával című [témakört.](maintenance-notifications-portal.md)

Ez a lap azt ismerteti, hogy az Azure hogyan hajtja végre mindkét típusú karbantartást. A nem tervezett eseményekről (kimaradásokról) további információt [a Virtuális gépek Windows-hoz való elérhetőségének kezelése](./windows/manage-availability.md) vagy a megfelelő Linux cikk című témakörben [talál.](./linux/manage-availability.md)

A virtuális gépeken belül értesítéseket kaphat a közelgő karbantartásról a Windows vagy [Linux](./linux/scheduled-events.md) [ütemezett eseményeinek használatával.](./windows/scheduled-events.md)



## <a name="maintenance-that-doesnt-require-a-reboot"></a>Karbantartás, amely nem igényel újraindítást

A legtöbb platformfrissítés nincs hatással az ügyfél virtuális gépeire. Ha egy hatásmentes frissítés nem lehetséges, az Azure kiválasztja a frissítési mechanizmust, amely a legkevésbé befolyásolja az ügyfél virtuális gépek. 

A legtöbb nem nulla hatású karbantartás 10 másodpercnél rövidebb időre szünetelteti a virtuális gép. Bizonyos esetekben az Azure memória-megőrzési karbantartási mechanizmusokat használ. Ezek a mechanizmusok legfeljebb 30 másodpercig szüneteltetheti a virtuális gép, és megőrzi a memóriát a RAM-ban. A virtuális gép ezután folytatódik, és az óra automatikusan szinkronizálódik. 

A memóriamegőrző karbantartási munkák az Azure virtuális gépek több mint 90 százaléka számára. G, M, N és H sorozatban nem működik. Az Azure egyre gyakrabban használ élő-áttelepítési technológiákat, és javítja a memória-megőrzési karbantartási mechanizmusokat a szünet időtartamának csökkentése érdekében.  

Ezek a karbantartási műveletek, amelyek nem igényelnek újraindítást, egyszerre egy tartalék tartományt alkalmaznak. Leállnak, ha bármilyen figyelmeztető egészségügyi jelet kapnak. 

Az ilyen típusú frissítések hatással lehetnek egyes alkalmazásokra. Ha a virtuális gép élő-áttelepítés egy másik gazdagép, néhány bizalmas számítási feladatok előfordulhat, hogy egy kis teljesítménycsökkenés a virtuális gép szüneteltetése néhány perc alatt. A virtuális gépek karbantartására való felkészüléshez és az Azure karbantartása során gyakorolt hatás csökkentéséhez próbálja meg használni a Windows vagy [Linux](./linux/scheduled-events.md) [ütemezett eseményeket](./windows/scheduled-events.md) az ilyen alkalmazásokhoz. 

Van is egy funkció, karbantartás-vezérlés, a nyilvános előzetes verzió, amely segít a karbantartás kezeléséhez, amely nem igényel újraindítást. Azure [dedikált gazdagépeket](./linux/dedicated-hosts.md) vagy [egy elkülönített virtuális géphasználatát kell használnia.](../security/fundamentals/isolation-choices.md) A karbantartás-vezérlés lehetővé teszi, hogy kihagyja a platformfrissítéseket, és a frissítéseket a 35 napos gördülő ablakon belül alkalmazza. További információ: [Frissítések vezérlése karbantartás-vezérléssel és az Azure CLI.For](maintenance-control-cli.md)more information, see Control updates with Maintenance Control and the Azure CLI .


### <a name="live-migration"></a>Élő áttelepítés

Az élő áttelepítés olyan művelet, amely nem igényel újraindítást, és amely megőrzi a virtuális gép memóriáját. Ez okozza a szünet vagy fagyás, általában tartós nem több, mint 5 másodperc. A G, M, N és H sorozatkivételével minden infrastruktúra szolgáltatásként (IaaS) virtuális gép, jogosult az élő áttelepítésre. A jogosult virtuális gépek az Azure-flottában üzembe helyezett IaaS virtuális gépek több mint 90 százalékát képviselik. 

Az Azure platform a következő esetekben indítja el az élő áttelepítést:
- Tervezett karbantartás
- Hardverhiba
- Felosztás optimalizálása

Egyes tervezett karbantartási forgatókönyvek élő áttelepítést használnak, és az Ütemezett események segítségével előre megtudhatja, hogy mikor indulnak el az élő áttelepítési műveletek.

Az élő áttelepítés a virtuális gépek áthelyezésére is használható, ha az Azure Machine Learning-algoritmusok közelgő hardverhibát jeleznek előre, vagy ha optimalizálni szeretné a virtuális gép-foglalásokat. A leromlott hardverek példányait észlelő prediktív modellezésről további információt az [Azure virtuális gépek rugalmasságának javítása prediktív gépi tanulással és élő áttelepítéssel](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)című témakörben talál. Az élő áttelepítési értesítések megjelennek az Azure Portalon a Figyelő és a Szolgáltatás állapotnaplókban, valamint az ütemezett eseményekben, ha használja ezeket a szolgáltatásokat.

## <a name="maintenance-that-requires-a-reboot"></a>Újraindítást igénylő karbantartás

Abban a ritka esetben, ha a virtuális gépeket újra kell indítani a tervezett karbantartáshoz, előzetesértesítést kap. A tervezett karbantartás nak két szakasza van: az önkiszolgáló fázis és a tervezett karbantartási fázis.

Az *önkiszolgáló fázisban*, amely általában négy hétig tart, elindítja a karbantartást a virtuális gépeken. Az önkiszolgáló részeként lekérdezheti az egyes virtuális gépek állapotát és az utolsó karbantartási kérelem eredményét.

Az önkiszolgáló karbantartás indításakor a virtuális gép egy már frissített csomópontra kerül. Mivel a virtuális gép újraindul, az ideiglenes lemez elvész, és a virtuális hálózati adapterhez társított dinamikus IP-címek frissülnek.

Ha hiba merül fel az önkiszolgáló karbantartás során, a művelet leáll, a virtuális gép nem frissül, és megkapja a lehetőséget, hogy próbálja meg újra az önkiszolgáló karbantartás. 

Amikor az önkiszolgáló fázis véget ér, az *ütemezett karbantartási fázis* megkezdődik. Ebben a fázisban továbbra is lekérdezheti a karbantartási fázist, de nem tudja elindítani a karbantartást saját maga.

Az újraindítást igénylő karbantartás kezeléséről az Azure [CLI,](maintenance-notifications-cli.md)a [PowerShell](maintenance-notifications-powershell.md) vagy a portál használatával **tervezett karbantartási értesítések kezelése** című [témakörben](maintenance-notifications-portal.md)talál. 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Rendelkezésre állási szempontok az ütemezett karbantartás során 

Ha úgy dönt, hogy megvárja az ütemezett karbantartási fázist, néhány dolgot érdemes megfontolnia a virtuális gépek legmagasabb rendelkezésre állásának fenntartásához. 

#### <a name="paired-regions"></a>Párosított régiók

Minden Azure-régió párosítva van egy másik régióval ugyanazon a földrajzi környezeten belül. Együtt, hogy egy régió pár. Az ütemezett karbantartási fázisban az Azure csak a régiópár egyetlen régiójában frissíti a virtuális gépeket. Például a virtuális gép frissítése az USA északi középső részén, az Azure nem frissíti a virtuális gép az USA déli középső részén egy időben. Azonban más régiók (például Észak-Európa) karbantarthatók ugyanabban az időben, mint az USA keleti régiója. A régiópárok működésének megismerése segíthet a virtuális gépek régiók közötti elosztásában. További információ: [Azure régiópárok.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)

#### <a name="availability-sets-and-scale-sets"></a>Rendelkezésre állási csoportok és méretezési csoportok

Amikor egy számítási feladatok üzembe helyezése az Azure virtuális gépeken, létrehozhatja a virtuális gépek egy *rendelkezésre állási készlet* magas rendelkezésre állású az alkalmazás számára. A rendelkezésre állási csoportok használatával biztosíthatja, hogy egy kimaradás i vagy karbantartási események újraindítást igénylő, legalább egy virtuális gép érhető el.

Egy rendelkezésre állási csoporton belül az egyes virtuális gépek legfeljebb 20 frissítési tartományban vannak elosztva. Az ütemezett karbantartás során egy adott időpontban csak egy frissítési tartomány frissül. A tartományok frissítése nem feltétlenül folyamatosan frissül. 

A *virtuálisgép-méretezési csoportok* egy Azure-számítási erőforrás, amely segítségével üzembe helyezheti és kezelheti az azonos virtuális gépek egyetlen erőforrásként készletét. A méretezési csoport automatikusan üzembe helyezhető az ud-k között, például egy rendelkezésre állási csoportban lévő virtuális gépek. A rendelkezésre állási csoportokhoz, méretezési csoportok használatakor csak egy UD frissül egy adott időpontban az ütemezett karbantartás során.

A virtuális gépek magas rendelkezésre állásra való beállításáról a [Virtuális gépek Windows-hoz való elérhetőségének kezelése](./windows/manage-availability.md) vagy a megfelelő [Linux-cikk](./linux/manage-availability.md)című témakörben olvashat bővebben.

#### <a name="availability-zones"></a>Rendelkezésre állási zónák

A rendelkezésre állási zónák egyedi fizikai helyek egy Azure-régióban. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosítása érdekében legalább három különálló zóna van az összes engedélyezett régióban. 

A rendelkezésre állási zóna egy tartalék tartomány és egy frissítési tartomány kombinációja. Ha három vagy több virtuális gépet hoz létre egy Azure-régió három zónájában, a virtuális gépek hatékonyan eloszthatók három tartalék tartomány és három frissítési tartomány között. Az Azure platform felismeri ezt a terjesztési frissítési tartományok között, hogy győződjön meg arról, hogy a különböző zónákban lévő virtuális gépek nem frissülnek egyszerre.

Minden infrastruktúra-frissítés zónanként, egyetlen régión belül gördül ki. De lehet, hogy a telepítés az 1-es zónában történik, és a 2-es zónában, egyidejűleg más központi telepítés is előfordulhat. A központi telepítések nem minden szerializált. De egy egyetlen üzembe helyezés egyszerre csak egy zónát gördít ki a kockázat csökkentése érdekében.

## <a name="next-steps"></a>További lépések 

Használhatja az [Azure CLI,](maintenance-notifications-cli.md) [Azure PowerShell](maintenance-notifications-powershell.md) vagy a [portál](maintenance-notifications-portal.md) a tervezett karbantartás kezeléséhez. 