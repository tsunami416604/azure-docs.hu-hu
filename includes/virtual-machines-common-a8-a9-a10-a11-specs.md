

## <a name="deployment-considerations"></a>Telepítési szempontok
* **Azure-előfizetés** – a használatalapú előfizetés vagy egyéb beszerzési lehetőségek csak néhány számítási igényű példányok telepítése érdekében. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.

* **Tarifa- és rendelkezésre állás** -ezek a Virtuálisgép-méretek érhető el csak a Standard tarifacsomagot. Ellenőrizze a [régió elérhető termékek] (https://azure.microsoft.com/regions/services/) a Azure-régiók rendelkezésre állás érdekében. 
* **Magok kvóta** – szükség lehet az Azure-előfizetéshez az alapértelmezett érték a magok kvótájának növeléséhez. Az előfizetés is lehet, hogy korlátozza az egyes virtuális gép mérete családok, többek között a H-sorozat telepítheti magok száma. A kvóta növelését [nyissa meg az online támogatás ügyfélkérés](../articles/azure-supportability/how-to-create-azure-support-request.md) díjmentesen. (Alapértelmezett korlátokat eltérhetnek attól függően, hogy az előfizetés kategória.)
  
  > [!NOTE]
  > Ha nagyméretű kapacitási igényeihez van, forduljon az Azure támogatási szolgálatához. Azure-kvótákról jóváírás korlátozza, nem a kapacitás garanciát. Függetlenül attól, a kvóta van csak szó, a magok használatát.
  > 
  > 
* **Virtuális hálózati** – az Azure [virtuális hálózati](https://azure.microsoft.com/documentation/services/virtual-network/) a számítási igényű példányok használatához nem szükséges. Azonban a számos olyan telepítése esetén kell legalább egy felhőalapú Azure virtuális hálózat vagy a hely-hely kapcsolatot, ha a helyszíni erőforrások eléréséhez szükséges. Szükség esetén hozzon létre egy új virtuális hálózat a példányok telepítése. Az affinitáscsoportokban lévő virtuális hálózathoz számítási igényű virtuális gépek hozzáadása nem támogatott.
* **Átméretezése** – miatt a speciális hardverre átméretezése csak az ugyanazon mérete család (H-méretek és számítási igényű A-sorozatú) számítási igényű példányokat. Például csak átméretezheti H-sorozat VM egy H sorozatnak megfelelő méretűre egy másikra. A számítási igényű méretre eltér egy nem számítási intenzív átméretezése ezenkívül nem támogatott.  
