Ha még nem tette meg, beszerezheti az Ön [Azure-fiókjához kapcsolt](../articles/xplat-cli-connect.md) [Azure-előfizetés ingyenes próbaverzióját](https://azure.microsoft.com/pricing/free-trial/) és [az Azure parancssori felületét](../articles/xplat-cli-install.md). Az alábbiak szerint győződjön meg róla, hogy az Azure CLI Resource Manager-módban van:

```azurecli
azure config mode arm
```

Hozzon létre egy méretezési csoport a(z) `azure vmss quick-create` parancs használatával. A következő példa létrehoz egy `myVMSS` elnevezésű, Linux rendszerű méretezési csoportot 5 virtuálisgép-példánnyal a `myResourceGroup` nevű erőforráscsoportban:

```azurecli
azure vmss quick-create -n myVMSS -g myResourceGroup -l westus \
    -u ops -p P@ssw0rd! \
    -C 5 -Q Canonical:UbuntuServer:16.04.0-LTS:latest
```

A következő példa egy Windows rendszerű méretezési csoportot hoz létre ugyanazzal a konfigurációval:

```azurecli
azure vmss quick-create -n myVMSS -g myResourceGroup -l westus \
    -u ops -p P@ssw0rd! \
    -C 5 -Q MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest
```

A hely vagy a kép URN-jének testreszabásához használja az alábbi parancsokat: `azure location list` és `azure vm image {list-publishers|list-offers|list-skus|list|show}`.

A parancs futtatása után létrejön a méretezési készlet. A méretezési készlet egy NAT-szabállyal rendelkező terheléselosztót is tartalmaz, amely a terheléselosztó 50,000+i portját a virtuális gép 22-es portjához rendeli. A terheléselosztó FQDN-jének meghatározása után így ssh-kapcsolaton keresztül csatlakozhatunk a virtuális gépekhez:

```bash
# (if you decide to run this as a script, please invoke using bash)

# list load balancers in the resource group we created
#
# generic syntax:
# azure network lb list -g RESOURCE-GROUP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb list -g negatvmssrg | grep negatvmssrg)
split_line=( $line )
lb_name=${split_line[1]}

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is 
# associated to it
#
# generic syntax:
# azure network lb show -g RESOURCE-GROUP-NAME -n LOAD-BALANCER-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb show -g negatvmssrg -n $lb_name | grep loadBalancerFrontEnd)
split_line=( $line )
pip_name=${split_line[4]}

# now that we have the name of the public IP address, we can show the details to find the FQDN
#
# generic syntax:
# azure network public-ip show -g RESOURCE-GROUP-NAME -n PIP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network public-ip show -g negatvmssrg -n $pip_name | grep FQDN)
split_line=( $line )
FQDN=${split_line[3]}

# now that we have the FQDN, we can use ssh on port 50,000+i to connect to VM i (where i is 0-indexed)
#
# example to connct via ssh into VM "0":
ssh -p 50000 negat@$FQDN
```

<!--HONumber=Dec16_HO1-->


