<properties
    pageTitle="Ajánlat létrehozása az Azure Stackben | Microsoft Azure"
    description="Szolgáltatás-rendszergazdaként megtudhatja, hogyan hozhat létre ajánlatot bérlői számára az Azure Stackben."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/25/2016"
    ms.author="erikje"/>


# Ajánlat létrehozása az Azure Stackben

Az [ajánlatok](azure-stack-key-features.md#services-plans-offers-and-subscriptions) egy vagy több csomag olyan csoportjai, amelyet szolgáltatók nyújtanak a bérlőknek megvásárlásra (előfizetésre). Ebben a példában egy ajánlatot fog létrehozni, amely tartalmazza az utolsó lépésben [létrehozott csomagot](azure-stack-create-plan.md). Ez lehetőséget biztosít az ajánlat előfizetőinek a virtuális gépek létrehozására.

1.  [Jelentkezzen be](azure-stack-connect-azure-stack.md#log-in-as-a-service-administrator) a portálra szolgáltatás-rendszergazdaként.
    ![](media/azure-stack-create-offer/image1.png)

2.  Kattintson az **Új** lehetőségre.

3.  Kattintson a **Bérlői ajánlatok és csomagok**, majd az **Ajánlat** elemre.
    ![](media/azure-stack-create-offer/image2.png)

4.  Az **Új ajánlat** panelen hajtsa végre a következőket:

    1.  Töltse ki a **Megjelenítendő név** és **Erőforrás neve** mezőt. A megjelenítendő név az ajánlat rövid neve. Kizárólag a rendszergazda láthatja az erőforrás nevét. Ezt a nevet használják a rendszergazdák az ajánlattal mint Azure Resource Manager-erőforrással végzett műveletekhez.

    2.  Válasszon ki egy új vagy egy létező **erőforráscsoportot**.

        ![](media/azure-stack-create-offer/image3.png)

5.  Kattintson az **Alapcsomagok** elemre, majd a **Csomag** panelen válassza ki azokat a csomagokat, amelyeket bele szeretne foglalni az ajánlatba, végül kattintson a **Kiválasztás** elemre. Az ajánlat létrehozásához kattintson a **Létrehozás** parancsra.

    ![](media/azure-stack-create-offer/image4.png)

6.  Kattintson az **Állapot módosítása**, majd a **Nyilvános** elemre.
A csomagokat és ajánlatokat nyilvánossá kell tenni a bérlők számára, hogy az előfizetés során láthassák a rendelkezésükre álló lehetőségeket. Ha a csomag magánjellegű, de az ajánlat nyilvános, a bérlők hozzáférhetnek az ajánlathoz, de nem látják a csomag részleteit. A csomagok és ajánlatok típusa a következő lehet:

    -   **Nyilvános:** Látható a bérlők számára.

    -   **Magánjellegű:** Kizárólag a szolgáltatás-rendszergazdák számára látható. Csomag vagy ajánlat tervezése közben lehet hasznos, vagy ha a szolgáltatás-rendszergazda szeretne egyenként jóváhagyni minden előfizetést.

    -   **Leszerelve:** Új előfizetők már nem választhatják. A szolgáltatás-rendszergazda használhatja a leszerelve beállítást akkor, ha meg szeretné akadályozni az új előfizetések létrejöttét, de meg szeretné hagyni az aktuális előfizetőket.

    ![](media/azure-stack-create-offer/image6.png)

A csomagon vagy ajánlaton végzett módosítások nem láthatók azonnal a bérlők számára. A módosítások megtekintéséhez az előfizetés állapotának InSync-nek kell lennie, majd a bérlőnek frissítenie kell a portált, vagy kijelentkeznie, és újból bejelentkeznie.

Egy kiegészítő előfizetés létrehozása és az állapot InSync értékre állítása után is előfordulhat, hogy ki kell jelentkeznie, majd újból be kell jelentkeznie az új előfizetések megtekintéséhez az „Előfizetésválasztóban” új erőforrások vagy erőforráscsoportok létrehozásakor.

## Következő lépések

[Előfizetés egy ajánlatra, majd egy virtuális gép létrehozása](azure-stack-subscribe-plan-provision-vm.md)



<!--HONumber=Sep16_HO3-->


