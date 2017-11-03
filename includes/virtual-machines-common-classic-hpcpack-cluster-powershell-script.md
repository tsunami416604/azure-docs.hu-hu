



Attól függően, hogy a környezet és a lehetőségek a parancsfájl az összes a fürt infrastruktúráról, beleértve az Azure-beli virtuális hálózat, storage-fiókok, felhőszolgáltatások, tartományvezérlő, helyi vagy távoli SQL-adatbázisok, átjárócsomópont és további fürtcsomópontokat hozhat létre. A parancsfájl azt is megteheti, használja a már meglévő Azure-infrastruktúra és csak a HPC fürtcsomópontok létrehozása.

Egy HPC Pack fürt tervezésével kapcsolatos háttér-információkat, tekintse meg a [a termék próbaváltozata és tervezése](https://technet.microsoft.com/library/jj899596.aspx) és [bevezetés](https://technet.microsoft.com/library/jj899590.aspx) a HPC Pack 2012 R2 TechNet könyvtár tartalmának.

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**: előfizetés használhatja az Azure globális vagy Azure Kína szolgáltatásban. Az előfizetési korlátozásait számát és típusát telepítése fürtcsomópontok hatással. További információ: [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../articles/azure-subscription-service-limits.md).
* **Az Azure PowerShell 0.8.10 Windows-ügyfélszámítógéppel vagy később telepített és konfigurált**: lásd: [Ismerkedés az Azure PowerShell](/powershell/azureps-cmdlets-docs) a telepítési utasításokat és a lépéseket az Azure-előfizetéssel való kapcsolódáshoz.
* **HPC Pack IaaS telepítési parancsfájl**: Töltse le és csomagolja ki a parancsfájlt a legújabb verzióját a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). A verziószám a parancsfájl futtatásával `New-HPCIaaSCluster.ps1 –Version`. Ez a cikk a parancsfájl 4.5.2-es verziójának alapul.
* **Parancsfájl konfigurációs fájlja**: hozzon létre egy XML-fájl, amely a HPC-fürt konfigurálásához használja a parancsfájl. Ehhez útmutatást és példákat című cikkben és a fájl, amely a telepítési parancsfájl társul Manual.rtf szakaszban talál.

## <a name="syntax"></a>Szintaxis
```PowerShell
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
> [!NOTE]
> A parancsfájl futtatása rendszergazdaként.
> 
> 

### <a name="parameters"></a>Paraméterek
* **ConfigFile**: megadásával írhatja le a HPC-fürt konfigurációs fájljának elérési útja. További információ a konfigurációs fájl ebben a témakörben, vagy a fájl a mappában Manual.rtf parancsfájlt tartalmazó.
* **AdminUserName**: felhasználónevét adja meg. Ha a tartomány erdő hozta létre a parancsfájlt, ez lesz az összes virtuális gép helyi rendszergazda felhasználónevét és a tartomány-rendszergazdai nevet. Ha a tartomány erdő már létezik, ez adja meg a tartományi felhasználót a HPC csomag telepítéséhez helyi rendszergazdai felhasználónév.
* **AdminPassword**: Adja meg a rendszergazdai jelszót. Ha a parancssorban nincs megadva, a parancsprogram kéri a jelszót kér a felhasználótól.
* **HPCImageName** (nem kötelező): a HPC-fürt telepítéséhez használandó HPC Pack méretű kép neve. A Microsoft által biztosított HPC Pack lemezkép az Azure piactérről kell lennie. Ha nincs megadva (általában ajánlott), a parancsfájl úgy dönt, a legutóbbi közzétett [HPC Pack 2012 R2 rendszerképnél](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/). A legutóbbi lemezkép a Windows Server 2012 R2 Datacenter HPC Pack 2012 R2 Update 3 telepítve alapul.
  
  > [!NOTE]
  > Központi telepítés sikertelen lesz, ha nem adja meg egy érvényes HPC Pack lemezképet.
  > 
  > 
* **Naplófájl** (nem kötelező): Adja meg a telepítési naplófájl elérési útja. Ha nincs megadva, a parancsfájl létrehoz egy naplófájlt az ideiglenes könyvtárban a parancsprogramot futtató számítógép.
* **Kényszerített** (nem kötelező): mellőzi a megerősítő megjelenő utasításokat.
* **NoCleanOnFailure** (nem kötelező): Adja meg, hogy a rendszer nem távolítja el az Azure virtuális gépeken, amelyek telepítése nem sikerült. A parancsfájl a telepítés folytatásához ismételt futtatása előtt manuálisan távolítsa el a virtuális gépeken, vagy a telepítés sikertelen lehet.
* **PSSessionSkipCACheck** (nem kötelező): minden felhőalapú szolgáltatás, a parancsfájl által központilag telepített virtuális gépek, az Azure által automatikusan generált önaláírt tanúsítványt, és a felhőszolgáltatás a virtuális gépek Windows távoli alapértelmezés szerint a tanúsítvány használatához Webszolgáltatások (WinRM) tanúsítvány. Azure virtuális gépeken a HPC-szolgáltatások telepítése, alapértelmezés szerint a parancsfájl átmenetileg telepíti ezeket a tanúsítványokat a helyi számítógép\\megbízható legfelső szintű hitelesítésszolgáltatók tárolójába, az ügyfélszámítógép arra, hogy letiltsa a "nem megbízható hitelesítésszolgáltató" biztonsági hiba parancsprogram végrehajtásakor. A tanúsítványokat a rendszer törli, a parancsfájl befejezése után. Ha ez a paraméter van megadva, a tanúsítványok nincsenek telepítve az ügyfélszámítógépen, és a biztonsági figyelmeztetés nem jelenik meg.
  
  > [!IMPORTANT]
  > Ez a paraméter nem ajánlott az üzemi környezetek.
  > 
  > 

### <a name="example"></a>Példa
Az alábbi példa létrehoz egy HPC Pack fürthöz, a konfigurációs fájl használata *MyConfigFile.xml*, és adja meg a fürt telepítéséhez rendszergazdai hitelesítő adatokat.

```PowerShell
.\New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### <a name="additional-considerations"></a>Néhány fontos megjegyzés
* A parancsfájl engedélyezheti a feladat elküldése a HPC Pack webes portál vagy a HPC Pack REST API használatával.
* A nem kötelező a parancsfájlt egyéni előtti és utáni konfigurációs parancsfájlokat az átjárócsomópont Ha azt szeretné, további szoftvereket telepíteni, vagy egyéb beállításainak konfigurálása.

## <a name="configuration-file"></a>Konfigurációs fájl
A telepítési parancsfájl konfigurációs fájlja egy XML-fájlt. A következő sémafájl HPCIaaSClusterConfig.xsd a HPC Pack IaaS telepítési parancsfájl mappában van. **IaaSClusterConfig** elem a konfigurációs fájl, amely tartalmazza a fájlt a telepítési parancsfájl mappában Manual.rtf részletes leírását lásd a gyermekelemek.

