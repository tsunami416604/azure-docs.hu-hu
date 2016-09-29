<properties
    pageTitle="Csatlakozás Microsoft Azure Stack POC-hoz | Microsoft Azure"
    description="További tudnivalók az Azure Stack POC portálhoz szolgáltatás-rendszergazdaként vagy bérlőként történő csatlakozásról."
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
    ms.date="08/01/2016"
    ms.author="erikje"/>


# Bejelentkezés az Azure Stack POC virtuális gépre

Az Azure Stack POC virtuális gépre a következő szerepeket használva jelentkezhet be:

- [**szolgáltatás-rendszergazda:**](#log-in-as-a-service-administrator) kezelheti az erőforrás-szolgáltatókat, bérlőajánlatokat, csomagokat, szolgáltatásokat, kvótákat és a díjszabást.

vagy

- [**bérlő:**](#log-in-as-a-tenant) létrehozhat, megfigyelhet és kezelhet olyan szolgáltatásokat, amelyekre előfizetett – ilyen például a Web Apps, a Storage és a Virtual Machines.

## Bejelentkezés szolgáltatás-rendszergazdaként

1.  Jelentkezzen be az Azure Stack POC fizikai gépre.

2.  Kattintson duplán a **ClientVM.AzureStack.local.rdp** asztali ikonra az ügyfél virtuális géphez tartozó távoli asztali kapcsolat megnyitásához.
 
    ![](media/azure-stack-connect-azure-stack/clientvmazurestacklocalicon.png)
    
    Ez automatikusan azt az AzureStack\\AzureStackUser fiókot használja, amelyet az üzembehelyezési parancsfájl hozott létre. A szkriptfolyamat 5. lépésében megadott rendszergazdai jelszót használja **A beépített rendszergazdai jelszó megadása** üzenetnél.

3.  A ClientVM.AzureStack.local asztalon kattintson duplán a **Microsoft Azure Stack POC Portal** ikonra (https://portal.azurestack.local/) a [portál](azure-stack-key-features.md#portal) megnyitásához.

    ![](media/azure-stack-connect-azure-stack/microsoftazurestackpocprtalicon.png)

4.  Jelentkezzen be szolgáltatás-rendszergazda fiókkal.

## Bejelentkezés bérlőként

A szolgáltatás-rendszergazda bejelentkezhet bérlőként azon csomagok, ajánlatok és előfizetések teszteléséhez, amelyeket a bérlői használhatnak.
Ha még nem rendelkezik ilyennel, [hozzon létre egy bérlői fiókot](azure-stack-add-new-user-aad.md) a bejelentkezés előtt.

1.  Jelentkezzen be az Azure Stack fizikai gépre.

2.  Kattintson duplán a **ClientVM.AzureStack.local.rdp** asztali ikonra az ügyfél virtuális géphez tartozó távoli asztali kapcsolat megnyitásához. 

    ![](media/azure-stack-connect-azure-stack/clientvmazurestacklocalicon.png)

    Ez automatikusan azt az AzureStack\\AzureStackUser fiókot használja, amelyet az üzembehelyezési parancsfájl hozott létre. A szkriptfolyamat 5. lépésében megadott rendszergazdai jelszót használja **A beépített rendszergazdai jelszó megadása** üzenetnél.

3.  A ClientVM.AzureStack.local asztalon kattintson duplán a **Microsoft Azure Stack POC Portal** ikonra (https://portal.azurestack.local/) a [portál](azure-stack-key-features.md#portal) megnyitásához.

    ![](media/azure-stack-connect-azure-stack/microsoftazurestackpocprtalicon.png)

4.  Jelentkezzen be a bérlői fiókkal.

Az RDP korlátozhatja, hogy hány felhasználó érheti el a fizikai Microsoft Azure POC-állomást. Több felhasználó engedélyezéshez tekintse meg a [Több egyidejű felhasználói kapcsolat engedélyezése](azure-stack-enable-multiple-concurrent-users.md) című részt.

## Következő lépések

[Első feladatok](azure-stack-first-scenarios.md)



<!--HONumber=Sep16_HO3-->


