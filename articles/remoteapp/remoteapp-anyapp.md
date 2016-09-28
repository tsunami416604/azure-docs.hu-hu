<properties
   pageTitle="Windows-alkalmazások futtatása bármely Azure RemoteAppet használó eszközön | Microsoft Azure"
   description="Ismerje meg, hogyan oszthatja meg bármelyik Windows-alkalmazást a felhasználóival az Azure RemoteApp segítségével."
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>


# Windows-alkalmazások futtatása bármely Azure RemoteAppet használó eszközön

> [AZURE.IMPORTANT]
> Azure RemoteApp hamarosan megszűnik. A részletekért olvassa el a [bejelentést](https://go.microsoft.com/fwlink/?linkid=821148).

Bárhol, bármilyen eszközön, akár azonnal futtathat Windows-alkalmazásokat – egyszerűen az Azure RemoteApp használatával. Legyen szó akár egy 10 éve írt egyéni alkalmazásról, akár egy Office-alkalmazásról, felhasználói nincsenek többé egy adott operációs rendszerhez (például a Windows XP-hez) kötve néhány alkalmazás miatt.

Az Azure RemoteApp segítségével a felhasználók a saját Android- vagy Apple-készülékeiken is ugyanazt a felhasználói élményt élvezhetik, mint Windows rendszeren (vagy Windows Phone-telefonokon). Ehhez a Windows-alkalmazását Windows rendszerű virtuális gépeken tároljuk az Azure-ban, így a felhasználók bárhonnan elérhetik ezeket, ha rendelkeznek internetkapcsolattal. 

A továbbiakban egy példát mutatunk be arra, hogy ezt pontosan hogyan teheti meg.

Ebben a cikkben az Access programot fogjuk megosztani az összes felhasználónkkal. Azonban ehelyett BÁRMELYIK alkalmazás használható. Ha alkalmazását Windows Server 2012 R2 rendszerű számítógépre telepíti, az alábbi lépésekkel mindig megoszthatja azt. Tekintse át az [alkalmazás követelményeit](remoteapp-appreqs.md), és ellenőrizze, hogy működni fog-e.

Fontos megjegyezni, hogy mivel az Access egy adatbázis, és szeretnénk, hogy az adatbázis használható legyen, néhány további lépést is elvégzünk, hogy a felhasználók hozzáférhessenek az Access adatmegosztásához. Ha az Ön alkalmazása nem adatbázis, vagy nincs szükség arra, hogy a felhasználók hozzáférhessenek egy adatmegosztáshoz, akkor az oktatóanyag erre vonatkozó lépéseit kihagyhatja.

> [AZURE.NOTE] <a name="note"></a>A jelen oktatóanyag elvégzéséhez Azure-fiókra van szükség:
> - [Ingyenesen is létrehozhat egy Azure-fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F): A kapott kreditek használatával kipróbálhatja a fizetős Azure-szolgáltatásokat, sőt, azok lejárta után is megtarthatja a fiókot, és továbbra is használhatja az ingyenes Azure-szolgáltatásokat (amilyen például a Websites). A bankkártyáját semmilyen költség nem terheli, hacsak Ön kifejezetten nem módosítja beállításait ennek engedélyezéséhez.
> - [Aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): Az MSDN-előfizetés minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat.


## Katalógus létrehozása a RemoteAppben

Első lépésként hozzon létre egy gyűjteményt. Ez a gyűjtemény lesz az alkalmazásai és a felhasználói tárolója. Minden gyűjtemény egy rendszerképen alapul – létrehozhatja a saját rendszerképét, vagy használhatja azt, amelyet az előfizetéséhez kapott. Ebben az oktatóanyagban az Office 2013 próbaverziójának rendszerképét használjuk – ebben található az alkalmazás, amelyet meg szeretnénk osztani.

1. Az Azure portálon görgessen le a bal oldali navigációs fában, amíg meg nem jelenik a RemoteApp. Nyissa meg azt az oldalt.
2. Kattintson a **RemoteApp-gyűjtemény létrehozása** lehetőségre.
3. Kattintson a **Gyors létrehozás** gombra, és adjon meg egy nevet a gyűjtemény számára.
4. Válassza ki a gyűjtemény létrehozásához használni kívánt régiót. A lehető legjobb élmény érdekében azt a régiót válassza, amely földrajzilag a legközelebb esik ahhoz a helyhez, ahonnan a felhasználói elérik majd az alkalmazást. Például ebben az oktatóanyagban a felhasználók Redmondban, Washington államban találhatóak. Az ehhez legközelebbi Azure-régió az **USA nyugati régiója**.
5. Válassza ki a használni kívánt számlázási csomagot. Az alapszintű számlázási csomag 16 felhasználót helyez egy nagyméretű Azure virtuális gépre, míg a standard számlázási csomag esetében 10 felhasználó található egy nagyméretű Azure virtuális gépen. Általában az alapszintű csomag adatbevitel típusú munkafolyamatok esetében működik jól. Egy termelékenységi alkalmazás, például az Office mellé a standard tervet érdemes választani.
6. Végül válassza ki az Office 2013 Professional rendszerképet. Ez a rendszerkép tartalmazza Office 2013-alkalmazásokat. Emlékeztető: ez a rendszerkép csak próbaverziójú gyűjteményekhez és POC-khez használható, éles gyűjteményekben nem.
7. Most kattintson a **RemoteApp-gyűjtemény létrehozása** lehetőségre.

![Felhőalapú gyűjtemény létrehozása a RemoteAppben](./media/remoteapp-anyapp/ra-anyappcreatecollection.png)

Ez elindítja a gyűjtemény létrehozását, de a folyamat akár egy órát is igénybe vehet.

Most már készen áll arra, hogy hozzáadja a felhasználóit.

## Az alkalmazás megosztása a felhasználókkal

Miután a gyűjteményt sikeresen létrehozta, ideje közzétenni az Accesst a felhasználók számára, és megadni, hogy mely felhasználók férhessenek hozzá.

Ha a gyűjtemény létrehozása közben elnavigált az Azure RemoteApp csomóponttól, akkor az Azure kezdőlapról lépjen vissza oda.

2. Kattintson a korábban létrehozott gyűjteményre a további beállítások eléréséhez és a gyűjtemény konfigurálásához.
![Egy új felhőalapú RemoteApp-gyűjtemény](./media/remoteapp-anyapp/ra-anyappcollection.png)
3. A **Közzététel** lapon kattintson a képernyő alján látható **Közzététel** elemre, majd kattintson **Start menü programjainak közzététele** lehetőségre.
![RemoteApp-program közzététele](./media/remoteapp-anyapp/ra-anyapppublish.png)
4. Válassza ki a listából a közzétenni kívánt alkalmazásokat. Ebben az oktatóanyagban az Accesst választjuk. Kattintson a **Befejezés** gombra. Várja meg, míg az alkalmazások közzététele befejeződik.
![Az Access közzététele a RemoteAppben](./media/remoteapp-anyapp/ra-anyapppublishaccess.png)


1. Miután az alkalmazás közzététele befejeződött, lépjen a **Felhasználói hozzáférés** lapra, és vegye fel az összes felhasználót, akinek hozzáférésre van szüksége az alkalmazásokhoz. Adja meg a felhasználók felhasználónevét (e-mail-címét), majd kattintson a **Mentés** gombra.

![Felhasználók hozzáadása a RemoteApphez](./media/remoteapp-anyapp/ra-anyappaddusers.png)


1. Itt az ideje értesíteni a felhasználókat az új alkalmazásokról, és arról, hogyan érhetik el ezeket. Ehhez küldjön a felhasználóinak egy e-mailt, amely a távoli asztali ügyfél letöltési URL-címére irányítja őket.
![A RemoteApp ügyféloldali letöltési URL-címe](./media/remoteapp-anyapp/ra-anyappurl.png)

## Az Access elérésének konfigurálása

Egyes alkalmazások további konfigurálást igényelnek azután, hogy üzembe helyezte őket a RemoteAppen keresztül. Az Access esetében egy fájlmegosztást fogunk létrehozni az Azure-ban, amelyhez bármely felhasználó hozzáférhet. (Ha ezt nem szeretné megtenni, a felhőalapú gyűjteményünk helyett létrehozhat egy [hibrid gyűjteményt](remoteapp-create-hybrid-deployment.md), amely lehetővé teszi, hogy a felhasználók a helyi hálózaton férhessenek hozzá fájlokhoz és információkhoz.) Ezután meg kell kérnünk a felhasználóinkat, hogy a számítógépük egyik helyi lemezét képezzék le az Azure-fájlrendszerre.

Az első részt Ön mint a rendszergazda végzi el. Ezután néhány lépést a felhasználóinak kell elvégeznie.

1. Először tegye közzé a parancssori felületet (cmd.exe). A **Közzététel** lapon válassza a **cmd** elemet, majd kattintson a **Közzététel > Program közzététele elérési út használatával** lehetőségre.
2. Adja meg az alkalmazás nevét és az elérési utat. Ebben az esetben a név legyen „Fájlkezelő”, az elérési út pedig „%SYSTEMDRIVE%\windows\explorer.exe”.
![Tegye közzé a cmd.exe fájlt.](./media/remoteapp-anyapp/ra-publishcmd.png)
3. Most hozzon létre egy Azure [Storage-fiókot](../storage/storage-create-storage-account.md). Mi az „accessstorage” nevet adtuk a sajátunknak, válasszon Ön is valamilyen beszédes nevet. (A Hegylakó után szabadon: csak egy „accessstorage” maradhat.)![Az Azure Storage-fiókunk](./media/remoteapp-anyapp/ra-anyappazurestorage.png)
4. Most lépjen vissza az irányítópultra, és nézze meg a tároló elérési útját (végponthelyét). Erre később még szükség lesz, ezért másolja ki valahová.
![A tárfiók elérési útja](./media/remoteapp-anyapp/ra-anyappstoragelocation.png)
5. Miután a tárfiók elkészült, a következő lépésben az elsődleges elérési kulcsra lesz szükség. Kattintson az **Elérési kulcsok kezelése** lehetőségre, majd másolja ki az elsődleges elérési kulcsot.
6. Ezután állítsa be a tárfiók környezetét, és hozzon létre egy új fájlmegosztást az Access számára. Futtassa a következő parancsmagokat egy emelt szintű Windows PowerShell-ablakban:

        $ctx=New-AzureStorageContext <account name> <account key>
        $s = New-AzureStorageShare <share name> -Context $ctx

    Mi ezeket a parancsmagokat futtatjuk le:

        $ctx=New-AzureStorageContext accessstorage <key>
        $s = New-AzureStorageShare <share name> -Context $ctx


Ezután a felhasználókon a sor. Először kérje meg a felhasználóit, hogy telepítsenek egy [RemoteApp-ügyfelet](remoteapp-clients.md). Ezután a felhasználóknak hozzá kell rendelniük egy meghajtót a fiókjukból az Ön által létrehozott Azure-fájlmegosztáshoz, és hozzá kell adniuk az Access-fájljaikat. Ezt a következőképpen tehetik meg:

1. A RemoteApp-ügyfélben nyissa meg a közzétett alkalmazások listáját. Indítsa el a cmd.exe programot.
2. Futtassa a következő parancsot a számítógép egy meghajtójának a fájlmegosztáshoz rendeléséhez:

        net use z: \\<accountname>.file.core.windows.net\<share name> /u:<user name> <account key>

    Ha a **/persistent** paramétert yes értékűre állítja, a meghajtó hozzárendelése minden munkamenetben megmarad.
1. Ezután indítsa el a Fájlkezelő alkalmazást a RemoteAppból. Másolja a fájlmegosztásba a megosztott alkalmazásban használni kívánt Access-fájlokat.
![Access-fájlok elhelyezése Azure-megosztásban](./media/remoteapp-anyapp/ra-anyappuseraccess.png)
1. Végül nyissa meg az Accesst, majd nyissa meg az imént megosztott adatbázist. Azt kellene látnia, hogy az Accessben az adatok a felhőből futnak.
![Egy igazi Access-adatbázis a felhőből futtatva](./media/remoteapp-anyapp/ra-anyapprunningaccess.png)

Most már bármelyik eszközön használhatja az Accesst, ha telepít hozzá egy RemoteApp-ügyfelet.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Következő lépések

Most, hogy elsajátította gyűjtemények létrehozását, próbáljon meg létrehozni egy [Office 365-öt használó gyűjteményt](remoteapp-tutorial-o365anywhere.md). Vagy hozzon létre egy [hibrid gyűjteményt](remoteapp-create-hybrid-deployment.md), amely hozzáfér a helyi hálózatához.

<!--Image references-->
 



<!--HONumber=Sep16_HO4-->


