---
title: "A virtuális gép számítási igényű Java-alkalmazás |} Microsoft Docs"
description: "Hozzon létre egy másik Java-alkalmazás figyelhető számítási igényű Java-alkalmazások futtató Azure virtuális géphez tudnivalók."
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: ae6f2737-94c7-4569-9913-d871450c2827
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: 8c51c0bb37e25ad61fe58a85dd641dabe0a1958c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-run-a-compute-intensive-task-in-java-on-a-virtual-machine"></a>Számításigényes feladat Javában történő futtatása virtuális gépen
> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

Az Azure-ral használhatja a virtuális gép számítási-igényes feladatok kezelésére. Például egy virtuális gép feladatokat és eredmények kézbesíthet ügyfélgépek vagy mobilalkalmazások. A cikk elolvasása után fog létrehozni egy virtuális gépet, amely egy másik Java-alkalmazás figyelhető számítási igényű Java-alkalmazás fut megértéséhez.

Ez az oktatóanyag feltételezi, tudja, hogyan kell létrehozni a Java-alkalmazások konzol, szalagtárak lehet importálni a Java-alkalmazást, és hozhat létre egy Java-archívum (JAR). Nem ismeri a Microsoft Azure feltételezi.

Az oktatóanyagban érintett témák köre:

* Hogyan hozhat létre egy virtuális gépet, a Java fejlesztői készlet (JDK) már telepítve.
* Távolról jelentkezzen be a virtuális géphez tudnivalók.
* How to create a service bus-névtér létrehozása.
* How to create a számítási-igényes feladattá végző Java-alkalmazás létrehozása.
* Megtudhatja, hogyan hozzon létre egy Java-alkalmazást, amely figyeli a számítási igényű feladat előrehaladását.
* A Java-alkalmazások futtatásának módját.
* Előfordulhat, hogy a Java-alkalmazások.

Ez az oktatóanyag a Traveling értékesítők probléma fogja használni, a számítás-igényes tevékenység. A Java-alkalmazások számítás-igényes feladat példát a következő:

![Traveling értékesítők probléma solver][solver_output]

A Java-alkalmazások figyelése a számítási-igényes tevékenység példát a következő:

![Traveling értékesítők probléma ügyfél][client_output]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Virtuális gép létrehozása
1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com).
2. Kattintson a **új**, kattintson a **számítási**, kattintson a **virtuális gép**, és kattintson a **a gyűjtemény**.
3. Az a **virtuális gép lemezképét válasszon** párbeszédpanelen jelölje ki **JDK 7 a Windows Server 2012**.
   Vegye figyelembe, hogy **JDK 6 Windows Server 2012** áll rendelkezésre, amennyiben örökölt alkalmazásokat, amelyek még nincsenek JDK 7 futtathatja.
4. Kattintson a **Tovább** gombra.
5. Az a **virtuálisgép-konfiguráció** párbeszédpanel:
   1. Adja meg a virtuális gép nevét.
   2. Adja meg a virtuális géphez használandó méretét.
   3. Adja meg a rendszergazdát a nevét a **felhasználónév** mező. Ez a név és a következő lép jelszó megjegyzése, azt fogja használni őket, amikor távolról jelentkezzen be a virtuális gép.
   4. Adja meg egy jelszót a **új jelszó** mezőben, majd adja meg újra a a **megerősítése** mező. Ez az a rendszergazdai fiók jelszavát.
   5. Kattintson a **Tovább** gombra.
6. A következő **virtuálisgép-konfiguráció** párbeszédpanel:
   1. A **felhőalapú szolgáltatás**, használja az alapértelmezett **hozzon létre egy új felhőalapú szolgáltatás**.
   2. A következő **felhőalapú szolgáltatás DNS-név** cloudapp.net belül egyedinek kell lennie. Szükség esetén módosítsa ezt az értéket úgy, hogy Azure azt jelzi, hogy egyedi.
   3. Adjon meg egy régiót, az affinitáscsoport vagy a virtuális hálózati. Ez az oktatóanyag céljából, adjon meg egy régiót például **USA nyugati régiója**.
   4. A **Tárfiók**, jelölje be **automatikusan létrehozott tárfiók használata**.
   5. A **rendelkezésre állási csoport**, jelölje be **(nincs)**.
   6. Kattintson a **Tovább** gombra.
7. A végső **virtuálisgép-konfiguráció** párbeszédpanel:
   1. Fogadja el az alapértelmezett végpont bejegyzéseket.
   2. Kattintson a **Befejezés** gombra.

## <a name="to-remotely-log-in-to-your-virtual-machine"></a>A távoli bejelentkezés a virtuális gép
1. Jelentkezzen be a [a klasszikus Azure portálon](https://manage.windowsazure.com).
2. Kattintson a **virtuális gépek**.
3. Kattintson a nevére, a virtuális gép, amelyet szeretne-e jelentkezni.
4. Kattintson a **Connect** (Csatlakozás) gombra.
5. Szükség esetén a virtuális géphez történő csatlakozáshoz, kövesse a megjelenő utasításokat. Amikor a rendszer kéri, a rendszergazda felhasználónevét és jelszavát, használja a virtuális gép létrehozásakor megadott értékeket.

Vegye figyelembe, hogy az Azure Service Bus funkció is megköveteli a Baltimore CyberTrust legfelső szintű tanúsítványt kell telepítenie a JRE részeként **cacerts** tárolja. Ez a tanúsítvány automatikusan tartalmazza a a Java-futtatókörnyezet (JRE) ebben az oktatóanyagban használják. Ha nincs ezt a tanúsítványt a a JRE **cacerts** tárolja, lásd: [tanúsítvány hozzáadása a Java hitelesítésszolgáltató tanúsítványtároló] [ add_ca_cert] azt (valamint a tanúsítványok megtekintése a cacerts tárolóban) hozzáadására vonatkozó információt.

## <a name="how-to-create-a-service-bus-namespace"></a>A service bus-névtér létrehozása
A Service Bus-üzenetsorok Azure-ban való használatához először létre kell hoznia egy szolgáltatásnévteret. A névtér egy hatókörkezelési tárolót biztosít az alkalmazáson belül a Service Bus erőforrásainak kezeléséhez.

Szolgáltatásnévtér létrehozása:

1. Jelentkezzen be a [a klasszikus Azure portálon](https://manage.windowsazure.com).
2. A klasszikus Azure portál bal alsó navigációs ablaktábláján kattintson **Service Bus, a hozzáférés-vezérlés és a gyorsítótár**.
3. A klasszikus Azure-portálon a bal felső ablaktáblán kattintson a **Service Bus** csomópontra, majd a **új** gombra.  
   ![Service Bus csomópont képernyőképe][svc_bus_node]
4. A a **hozzon létre egy új szolgáltatás Namespace** párbeszédpanelen adja meg egy **Namespace**, és annak ellenőrzése, hogy a rendszer egyedi, kattintson a **ellenőrizze a rendelkezésre állási** gombra.  
   ![Hozzon létre egy új Namespace képernyőképe][create_namespace]
5. Miután meggyőződött arról, hogy a névtér neve nem érhető el, válassza ki azt az országot vagy régiót, amelyben a névtér üzemeltetve lesz, és kattintson a **létrehozása Namespace** gombra.  
   
   A létrehozott névtér fog megjelenni a klasszikus Azure portálra, és aktiválja néhány percet vesz igénybe. Várjon, amíg az állapot **aktív** , mielőtt a következő lépéssel.

## <a name="obtain-the-default-management-credentials-for-the-namespace"></a>Az alapértelmezett felügyeleti hitelesítő adatok a névtér beszerzése
A várólista létrehozásakor az új névtéren, például a kezelési műveletek végrehajtásához kell szereznie a névtérhez tartozó felügyeleti hitelesítő adatokat.

1. A bal oldali navigációs ablaktáblán kattintson a **Service Bus** csomópontra az elérhető névterek listájának megjelenítéséhez.
   ![Elérhető névterek képernyőképe][avail_namespaces]
2. Jelölje ki az imént létrehozott a megjelenő listában névteret.
   ![Namespace lista képernyőképe][namespace_list]
3. A jobb oldali **tulajdonságok** ablaktábla listázza az új névtéren tulajdonságait.
   ![Képernyőfelvétel a Tulajdonságok panelen][properties_pane]
4. A **alapértelmezett kulcs** van-e rejtve. Kattintson a **nézet** gombra kattintva jelenítse meg a hitelesítő adatokat.
   ![Alapértelmezett kulcs képernyőképe][default_key]
5. Jegyezze fel a **alapértelmezett kibocsátó** és a **alapértelmezett kulcs** ezt az információt az alábbi műveletek elvégzését a névtér a rendszer használata.

## <a name="how-to-create-a-java-application-that-performs-a-compute-intensive-task"></a>Számítás-igényes feladattá végző Java-alkalmazás létrehozása
1. A fejlesztői számítógépén (amely kell lennie a létrehozott virtuális gépre) töltse le a [Javához készült Azure SDK](https://azure.microsoft.com/develop/java/).
2. Hozzon létre egy Java konzolalkalmazást, ez a szakasz végén a kód használatával. Ebben az oktatóanyagban fogjuk használni **TSPSolver.java** Java fájlneve. Módosítsa a **a\_szolgáltatás\_bus\_névtér**, **a\_szolgáltatás\_bus\_tulajdonos**, és **a\_szolgáltatás\_bus\_kulcs** helyőrzőket a service busszal használni **névtér**, **alapértelmezett kibocsátó** és **alapértelmezett kulcs** értékei, illetve.
3. Kódolási, miután az alkalmazás futtatható Java archív (JAR) exportálása, és a szükséges kódtárak csomagolni a generált JAR. Ebben az oktatóanyagban fogjuk használni **TSPSolver.jar** létrehozott JAR neveként.

<p/>

    // TSPSolver.java

    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import java.io.*;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;

    public class TSPSolver {

        //  Value specifying how often to provide an update to the console.
        private static long loopCheck = 100000000;  

        private static long nTimes = 0, nLoops=0;

        private static double[][] distances;
        private static String[] cityNames;
        private static int[] bestOrder;
        private static double minDistance;
        private static ServiceBusContract service;

        private static void buildDistances(String fileLocation, int numCities) throws Exception{
            try{
                BufferedReader file = new BufferedReader(new InputStreamReader(new DataInputStream(new FileInputStream(new File(fileLocation)))));
                double[][] cityLocs = new double[numCities][2];
                for (int i = 0; i<numCities; i++){
                    String[] line = file.readLine().split(", ");
                    cityNames[i] = line[0];
                    cityLocs[i][0] = Double.parseDouble(line[1]);
                    cityLocs[i][1] = Double.parseDouble(line[2]);
                }
                for (int i = 0; i<numCities; i++){
                    for (int j = i; j<numCities; j++){
                        distances[i][j] = Math.hypot(Math.abs(cityLocs[i][0] - cityLocs[j][0]), Math.abs(cityLocs[i][1] - cityLocs[j][1]));
                        distances[j][i] = distances[i][j];
                    }
                }
            } catch (Exception e){
                throw e;
            }
        }

        private static void permutation(List<Integer> startCities, double distSoFar, List<Integer> restCities) throws Exception {

            try
            {
                nTimes++;
                if (nTimes == loopCheck)
                {
                    nLoops++;
                    nTimes = 0;
                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.print("Current time is " + dateFormat.format(date) + ". ");
                    System.out.println(  "Completed " + nLoops + " iterations of size of " + loopCheck + ".");
                }

                if ((restCities.size() == 1) && ((minDistance == -1) || (distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-1)] < minDistance))){
                    startCities.add(restCities.get(0));
                    newBestDistance(startCities, distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-2)]);
                    startCities.remove(startCities.size()-1);
                }
                else{
                    for (int i=0; i<restCities.size(); i++){
                        startCities.add(restCities.get(0));
                        restCities.remove(0);
                        permutation(startCities, distSoFar + distances[startCities.get(startCities.size()-1)][startCities.get(startCities.size()-2)],restCities);
                        restCities.add(startCities.get(startCities.size()-1));
                        startCities.remove(startCities.size()-1);
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        private static void newBestDistance(List<Integer> cities, double distance) throws ServiceException, Exception {
            try
            {
                minDistance = distance;
                String cityList = "Shortest distance is "+minDistance+", with route: ";
                for (int i = 0; i<bestOrder.length; i++){
                    bestOrder[i] = cities.get(i);
                    cityList += cityNames[bestOrder[i]];
                    if (i != bestOrder.length -1)
                        cityList += ", ";
                }
                System.out.println(cityList);
                service.sendQueueMessage("TSPQueue", new BrokeredMessage(cityList));
            }
            catch (ServiceException se)
            {
                throw se;
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        public static void main(String args[]){

            try {

                Configuration config = ServiceBusConfiguration.configureWithWrapAuthentication(
                        "your_service_bus_namespace", "your_service_bus_owner",
                        "your_service_bus_key",
                        ".servicebus.windows.net",
                        "-sb.accesscontrol.windows.net/WRAPv0.9");

                service = ServiceBusService.create(config);

                int numCities = 10;  // Use as the default, if no value is specified at command line.
                if (args.length != 0)
                {
                    if (args[0].toLowerCase().compareTo("createqueue")==0)
                    {
                        // No processing to occur other than creating the queue.
                        QueueInfo queueInfo = new QueueInfo("TSPQueue");

                        service.createQueue(queueInfo);

                        System.out.println("Queue named TSPQueue was created.");

                        System.exit(0);
                    }

                    if (args[0].toLowerCase().compareTo("deletequeue")==0)
                    {
                        // No processing to occur other than deleting the queue.
                        service.deleteQueue("TSPQueue");

                        System.out.println("Queue named TSPQueue was deleted.");

                        System.exit(0);
                    }

                    // Neither creating or deleting a queue.
                    // Assume the value passed in is the number of cities to solve.
                    numCities = Integer.valueOf(args[0]);  
                }

                System.out.println("Running for " + numCities + " cities.");

                List<Integer> startCities = new ArrayList<Integer>();
                List<Integer> restCities = new ArrayList<Integer>();
                startCities.add(0);
                for(int i = 1; i<numCities; i++)
                    restCities.add(i);
                distances = new double[numCities][numCities];
                cityNames = new String[numCities];
                buildDistances("c:\\TSP\\cities.txt", numCities);
                minDistance = -1;
                bestOrder = new int[numCities];
                permutation(startCities, 0, restCities);
                System.out.println("Final solution found!");
                service.sendQueueMessage("TSPQueue", new BrokeredMessage("Complete"));
            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }
        }

    }



## <a name="how-to-create-a-java-application-that-monitors-the-progress-of-the-compute-intensive-task"></a>A számítási-igényes tevékenység kíséri Java-alkalmazások létrehozása
1. A fejlesztői gépen hozzon létre egy Java konzolalkalmazást, ez a szakasz végén a példakódot használatával. Ebben az oktatóanyagban fogjuk használni **TSPClient.java** Java fájlneve. Ahogy korábban is látható, módosítsa a **a\_szolgáltatás\_bus\_névtér**, **a\_szolgáltatás\_bus\_tulajdonos**, és **a\_szolgáltatás\_bus\_kulcs** helyőrzőket a service busszal használni **névtér**, **alapértelmezett kibocsátó** és **alapértelmezett kulcs** értékei, illetve.
2. Az alkalmazás futtatható JAR exportálása, és a szükséges kódtárak csomagolni a generált JAR. Ebben az oktatóanyagban fogjuk használni **TSPClient.jar** létrehozott JAR neveként.

<p/>

    // TSPClient.java

    import java.util.Date;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;

    public class TSPClient
    {

        public static void main(String[] args)
        {
                try
                {

                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.println("Starting at " + dateFormat.format(date) + ".");

                    String namespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    Configuration config;
                    config = ServiceBusConfiguration.configureWithWrapAuthentication(
                            namespace, issuer, key,
                            ".servicebus.windows.net",
                            "-sb.accesscontrol.windows.net/WRAPv0.9");

                    ServiceBusContract service = ServiceBusService.create(config);

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value is specified at command line.
                    if (args.length != 0)
                    {
                        waitMinutes = Integer.valueOf(args[0]);  
                    }

                    String waitString;

                    waitString = (waitMinutes == 1) ? "minute." : waitMinutes + " minutes.";

                    // This queue must have previously been created.
                    service.getQueue("TSPQueue");

                    int numRead;

                    String s = null;

                    while (true)
                    {

                        ReceiveQueueMessageResult resultQM = service.receiveQueueMessage("TSPQueue");
                        message = resultQM.getValue();

                        if (null != message && null != message.getMessageId())
                        {

                            // Display the queue message.
                            byte[] b = new byte[200];

                            System.out.print("From queue: ");

                            s = null;
                            numRead = message.getBody().read(b);
                            while (-1 != numRead)
                            {
                                s = new String(b);
                                s = s.trim();
                                System.out.print(s);
                                numRead = message.getBody().read(b);
                            }
                            System.out.println();
                            if (s.compareTo("Complete") == 0)
                            {
                                // No more processing to occur.
                                date = new Date();
                                System.out.println("Finished at " + dateFormat.format(date) + ".");
                                break;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            System.out.println("Queue is empty. Sleeping for another " + waitString);
                            Thread.sleep(60000 * waitMinutes);
                        }
                    }

            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }

        }

    }

## <a name="how-to-run-the-java-applications"></a>A Java-alkalmazások futtatása
A számítási igényű alkalmazást futtatva, először a várólista létrehozása, majd a utazás Saleseman probléma megoldására, amely az aktuális legjobb útvonalat ad hozzá a service bus-üzenetsorba. Míg a számítási igényű alkalmazás fut (vagy ezek után), futtassa az ügyfelet a service bus-üzenetsorba eredmények megtekintése céljából.

### <a name="to-run-the-compute-intensive-application"></a>A számítási igényű alkalmazás futtatásához
1. Jelentkezzen be a virtuális gépre.
2. Hozzon létre egy mappát, ahol az alkalmazás elindul. Például **c:\TSP**.
3. Másolás **TSPSolver.jar** való **c:\TSP**,
4. Hozzon létre egy fájlt **c:\TSP\cities.txt** a következő tartalommal.
   
        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33
5. A parancssorban módosítsa a könyvtárat c:\TSP.
6. Győződjön meg arról, a JRE bin mappájában megtalálható-e a PATH környezeti változóhoz.
7. Meg kell létrehoznia a Szolgáltató solver kombinációinak futtatása előtt a service bus-üzenetsorba. A következő parancsot a service bus-üzenetsorba létrehozásához.
   
        java -jar TSPSolver.jar createqueue
8. Most, hogy a várólista létrejön, a Szolgáltató solver kombinációinak is futtathatja. Például futtassa a következő parancsot, amely 8 várost solver futtatásához.
   
        java -jar TSPSolver.jar 8
   
   Ha nem adja meg egy számot, akkor a 10 városokat fog futni. Mivel a solver aktuális legrövidebb útvonalak talál, azt hozzáadja azokat a várakozási sorba.

> [!NOTE]
> Minél nagyobb a megadott számú, minél hosszabb a solver fog futni. Például futtató 14 városokat eltarthat néhány percig, és fut, amely 15 várost több óráig is eltarthat. 16 vagy több város tartozik a növekvő azt eredményezheti, hogy nap futtatókörnyezet (idővel hét, hónap és év). Ezt a várost növeli a számának értékelik ki a solver permutációk számát gyors növekedése okozza.
> 
> 

### <a name="how-to-run-the-monitoring-client-application"></a>A figyelési ügyfélalkalmazás futtatása
1. Jelentkezzen be a gépre, ha futtatja az ügyfélalkalmazás. Ez nem kell lennie a azonos számítógépen, amelyen a **TSPSolver** alkalmazás, bár lehet.
2. Hozzon létre egy mappát, ahol az alkalmazás elindul. Például **c:\TSP**.
3. Másolás **TSPClient.jar** való **c:\TSP**,
4. Győződjön meg arról, a JRE bin mappájában megtalálható-e a PATH környezeti változóhoz.
5. A parancssorban módosítsa a könyvtárat c:\TSP.
6. A következő parancsot.
   
        java -jar TSPClient.jar
   
    Szükség esetén adja meg, hány percig alvó állapotba Between a várólistára, a parancssori argumentum történő ellenőrzése. Az alapértelmezett alvó időszak ellenőrzéséhez a várólista: 3 percenként, amely akkor használatos, ha nincs parancssori argumentum átadott **TSPClient**. Ha egy másik értéket a alvó alatt használni kívánt, például egy perc, a következő parancsot.
   
        java -jar TSPClient.jar 1
   
    Az ügyfél fog futni, amíg azt egy üzenetet várólista a "Teljes". Ne feledje, hogy ha az ügyfél futtatása nélkül futtatja a solver többször, előfordulhat, hogy az ügyfél teljesen kiürítése a várólista többször is lefuthat. Másik lehetőségként törölnie a várólistát, és ezután hozza létre újra. A várólista törlése, futtassa a következő **TSPSolver** (nem **TSPClient**) parancsot.
   
        java -jar TSPSolver.jar deletequeue
   
    A solver fog futni, amíg be nem fejezi megvizsgálta az összes útvonal.

## <a name="how-to-stop-the-java-applications"></a>Előfordulhat, hogy a Java-alkalmazások
A solver mind a ügyfélalkalmazások lenyomhatja **Ctrl + C** való kilépéshez, ha meg szeretné szüntetni normál befejezése előtt.

[solver_output]:media/java-run-compute-intensive-task/WA_JavaTSPSolver.png
[client_output]:media/java-run-compute-intensive-task/WA_JavaTSPClient.png
[svc_bus_node]:media/java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg
[create_namespace]:media/java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[avail_namespaces]:media/java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[namespace_list]:media/java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg
[properties_pane]:media/java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg
[default_key]:media/java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg
[add_ca_cert]: ../../../java-add-certificate-ca-store.md
