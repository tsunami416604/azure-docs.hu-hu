## <a name="download-and-understand-the-arm-template"></a>Az ARM-sablon letöltése és megismerése
A GitHubból letöltheti a meglévő ARM-sablont, amellyel létrehozhat egy VNetet két alhálózattal, végrehajthatja a kívánt módosításokat, és újra felhasználhatja. Ehhez kövesse az alábbi lépéseket.

1. Nyissa meg a [mintasablon lapot](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Kattintson az **azuredeploy.json**, majd a **RAW** elemre.
3. Mentse a fájlt egy helyi mappába a számítógépén.
4. Ha már ismeri az ARM-sablonokat, akkor ugorjon a 7. lépéshez.
5. Nyissa meg az előbb mentett fájlt, és nézze meg az 5. sorban a **parameters** (paraméterek) alatt látható tartalmakat. Az ARM-sablonparaméterek az üzembe helyezés során kitölthető paraméterek helyőrzőiként működnek.
   
   | Paraméter | Leírás |
   | --- | --- |
   | **hely** |Az Azure-régió, ahol a VNet létrejön |
   | **vnetName** |Az új VNet neve |
   | **addressPrefix** |A VNet címtere CIDR-formátumban |
   | **subnet1Name** |Az első VNet neve |
   | **subnet1Prefix** |Az első alhálózat CIDR-blokkja |
   | **subnet2Name** |A második VNet neve |
   | **subnet2Prefix** |A második alhálózat CIDR-blokkja |
   
   > [!IMPORTANT]
   > A GitHubban fenntartott ARM-sablonok idővel módosulhatnak. Ne feledje el ellenőrizni a sablont, mielőtt használja.
   > 
   > 
6. Ellenőrizze a **resources** (erőforrások) alatt látható tartalmat, és figyelje meg a következőket:
   
   * **type**. A sablon által létrehozott erőforrástípus. Ebben az esetben a **Microsoft.Network/virtualNetworks**, ami egy VNetet jelöl.
   * **Név** Az erőforrás neve. Figyelje meg a **[parameters('vnetName')]** használatát, amely azt jelzi, hogy a nevet a felhasználó vagy egy paraméterfájl adja meg az üzembe helyezés során bemenetként.
   * **properties**. Az erőforrás tulajdonságainak listája. Ez a sablon a címteret és az alhálózati tulajdonságokat használja a VNet létrehozása során.
7. Lépjen vissza a [mintasablon lapra](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Kattintson az **azuredeploy-paremeters.json**, majd a **RAW** elemre.
9. Mentse a fájlt egy helyi mappába a számítógépén.
10. Nyissa meg az előbb mentett fájlt, és módosítsa a paraméterek értékeit. Az alábbi értékek használatával helyezze üzembe a forgatókönyvünkben leírt VNetet.
    
        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }
11. Mentse a fájlt.



<!--HONumber=Nov16_HO2-->


