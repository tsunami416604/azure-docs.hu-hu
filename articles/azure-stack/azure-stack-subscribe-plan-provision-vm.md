<properties
    pageTitle="Előfizetés egy ajánlatra, majd egy virtuális gép létrehozása az Azure Stackben (bérlőként) | Microsoft Azure"
    description="Megtudhatja, hogy bérlőként hogyan fizethet elő egy ajánlatra, és hogyan építhet ki virtuális gépet az Azure Stack szolgáltatásban."
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
    ms.date="08/15/2016"
    ms.author="erikje"/>


# Előfizetés egy ajánlatra

Most, hogy [létrehozott egy előfizetést](azure-stack-create-offer.md), tesztelje, hogy a bérlők létre tudnak-e hozni virtuális gépeket.

1.  Az Azure Stack POC számítógépen jelentkezzen be a `https://portal.azurestack.local` címre [bérlőként](azure-stack-connect-azure-stack.md#log-in-as-a-tenant), majd kattintson az **Előfizetés beszerzése** elemre.

    ![](media/azure-stack-subscribe-plan-provision-vm/image1.png)

2.  A **Megjelenítendő név** mezőben adja meg az előfizetés nevét.

    ![](media/azure-stack-subscribe-plan-provision-vm/image2.png)

3.  Kattintson az **Ajánlat** elemre, és ellenőrizze, hogy a létrehozott ajánlat szerepel-e az **Ajánlat választása** panelen. Kattintson az ajánlatra, a **Kiválasztás** elemre, majd az **OK** gombra.  

    ![](media/azure-stack-subscribe-plan-provision-vm/image3.png)

4.  A létrehozott előfizetés megtekintéséhez kattintson a **Tallózás**, majd az **Előfizetések** elemre.  

    ![](media/azure-stack-subscribe-plan-provision-vm/image4.png)

Ha sikeresen előfizetett egy ajánlatra, várja meg, amíg az előfizetés állapota InSync nem lesz. Ezután frissítse a portált, és tekintse meg, hogy mely szolgáltatások tartoznak az új előfizetéshez.




## Következő lépések

[Tárfiókok](azure-stack-provision-storage-account.md)



<!--HONumber=Sep16_HO3-->


