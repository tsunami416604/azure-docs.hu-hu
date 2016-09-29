<properties
    pageTitle="Labor létrehozása az Azure DevTest Labs szolgáltatásban | Microsoft Azure"
    description="Labor létrehozása virtuális gépekhez az Azure DevTest Labs szolgáltatásban"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/12/2016"
    ms.author="tarcher"/>


# Labor létrehozása az Azure DevTest Labs szolgáltatásban

## Előfeltételek

Labor létrehozásához a következőkre van szüksége:

- Azure-előfizetés. Az Azure megvásárlási lehetőségeinek ismertetése: [Az Azure megvásárlása](https://azure.microsoft.com/pricing/purchase-options/) vagy [Ingyenes egy hónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/). Az előfizetés tulajdonosának kell lennie a labor létrehozásához.

## A labor létrehozásának lépései az Azure DevTest Labs szolgáltatásban

A következő lépések bemutatják, hogyan használhatja az Azure Portalt labor létrehozására az Azure DevTest Labs szolgáltatásban. 

1. Jelentkezzen be az [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza a **További szolgáltatások**, majd a **DevTest Labs** elemet a listából.

1. A **DevTest Labs** panelen válassza a **Hozzáadás** lehetőséget.

    ![Labor hozzáadása](./media/devtest-lab-create-lab/add-lab-button.png)

1. A **Fejlesztési és tesztelési labor létrehozása** panelen:

    1. Adja meg a **Labor nevét**.
    
    1. Válassza ki a laborhoz társítani kívánt **Előfizetést**.
    
    1. Válassza ki a labor tárolásának **Helyét**.
    
    1. Válassza az **Automatikus leállítás** elemet annak megadásához, hogy engedélyezi-e az automatikus leállítást a labor összes virtuális gépénél (valamint az automatikus leállítás paramétereit is megadhatja).
    
    1. Válassza a **Tárolótípus** elemet a labor virtuális gépéhez tartozó tárolólemez típusának jelöléséhez. 
    
    1. Kattintson a **Létrehozás** gombra.

    ![Laborpanel létrehozása](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Következő lépések

A labor létrehozása után ezeket a lépéseket érdemes figyelembe venni:

- [Biztonságos hozzáférés a laborokhoz](devtest-lab-add-devtest-user.md).

- [Laborházirendek megadása](devtest-lab-set-lab-policy.md).

- [Laborsablon létrehozása](devtest-lab-create-template.md).

- [Egyéni összetevők létrehozása a virtuális géphez](devtest-lab-artifact-author.md).

- [Összetevőkkel rendelkező virtuális gép hozzáadása egy laborhoz](devtest-lab-add-vm-with-artifacts.md).


<!--HONumber=Sep16_HO4-->


