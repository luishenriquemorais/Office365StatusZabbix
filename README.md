# Office365StatusZabbix
Monitorar o status dos serviços do Office 365 com o Zabbix.

<hr size="10" width="100%">

Para verificarmos o status dos serviços basta acessar a URL: https://portal.office.com/servicestatus

<p align="center">
	<img src="src/images/Office365StatusZabbix1.0.png">
</p>

<hr size="10" width="100%">

Porem precisamos verificar a URL que esta página consome, para isto, clique com o botão direito em <b>Inspecionar</b>, clique em Network e depois aperte <b>F5</b> para que a página volte a carregar.


<p align="center">
	<img src="src/images/Office365StatusZabbix1.1.png">
</p>

<hr size="10" width="100%">

Com isso veremos uma item chamado <b>index</b> não <b>index.html</b>.
Clique em cima dele com o botão direito e logo após emcima de <b>Copy</b> e depois em <b>Copy link address</b>


<p align="center">
	<img src="src/images/Office365StatusZabbix1.2.png">
</p>

<hr size="10" width="100%">

Cole essa URL https://portal.office.com/api/servicestatus/index no browser e verá que irá carregar uma página em JSON.


<p align="center">
	<img src="src/images/Office365StatusZabbix1.3.png">
</p>

<hr size="10" width="100%">

Aqui um exemplo do JSON retornado no CMD.


<p align="center">
	<img src="src/images/Office365StatusZabbix1.4.png">
</p>

<hr size="10" width="100%">

Copie esse JSON e cole por exemplo no JSON Formatter https://jsonformatter.curiousconcept.com/# para deixa-lo melhor para visualizar.


<p align="center">
	<img src="src/images/Office365StatusZabbix1.5.png">
</p>

<hr size="10" width="100%">

Copie o resultado já formatado.


<p align="center">
	<img src="src/images/Office365StatusZabbix1.6.png">
</p>

<hr size="10" width="100%">

Cole no JSON Path http://jsonpath.com/ e na syntax veja como parsear somente o que desejar. Neste caso iremos parsear apenas os nomes.
<b>$.Services..Name</b><br>
O retorno é todas as posições name dentro do Array Services.


<p align="center">
	<img src="src/images/Office365StatusZabbix1.7.png">
</p>

<hr size="10" width="100%">
	
Uma forma de pegar apenas um item em vez do array todo é selecionar a posição dele dentro do Array, desta forma basta passar a posição dele dentro do syntax.<br>
<b>$.Services..[0].Name</b><br>
Neste caso o retorno é apenas <b>Outlook.com</b> dentro do Array Services.

	
<p align="center">
	<img src="src/images/Office365StatusZabbix1.8.png">
</p>

<hr size="10" width="100%">

<b>$.Services..[1].Name</b><br>
Neste caso o retorno é apenas <b>OneDrive</b> dentro do Array Services.

<p align="center">
	<img src="src/images/Office365StatusZabbix1.9.png">
</p>

<hr size="10" width="100%">

E caso queira verificar se ele está Up ou não basta filtrar no syntax apenas o <b>IsUp</b><br>
<b>$.Services..[1].IsUp</b><br>
Neste caso o retorno é apenas o status do <b>OneDrive</b> (true) dentro do Array Services.

<p align="center">
	<img src="src/images/Office365StatusZabbix1.10.png">
</p>

<hr size="10" width="100%">

Com essas informações agora podemos montar os sensores no Zabbix.<br>
Criamos um host chamado <b>Office 365 Status</b>.

<p align="center">
	<img src="src/images/Office365StatusZabbix1.11.png">
</p>

<hr size="10" width="100%">

Vamos explicar a criação dos itens exemplificando o item <b>Outlook</b>.<br>
<b>Name</b> -> Outlook.com <br>
<b>Type</b> -> HTTP agent <br>
<b>Key</b> -> outlook <br>
<b>URL</b> -> https://portal.office.com/api/servicestatus/index <br>

<p align="center">
	<img src="src/images/Office365StatusZabbix1.12.png">
</p>

<hr size="10" width="100%">

E em Preprocessing devemos adicionar dois steps. <br>
O primeiro é <b>JSONPath</b>. <br>
Com o parâmetro $.Services.[0].IsUp (É o valor de up ou dão do Outlook (Posição 0 do Array Services)). <br>

<p align="center">
	<img src="src/images/Office365StatusZabbix1.13.png">
</p>

<hr size="10" width="100%">


E o segundo step é <b>JavaScript</b> para transformar o texto true em um número para facilitar na criação de triggers. <br>
O script é esse: <br>

if (/true/.test(value)) { <br>
    return(1) <br>
}else{ <br>
      return(0) <br>
  } <br>
} <br>

<p align="center">
	<img src="src/images/Office365StatusZabbix1.14.png">
</p>

Feito tudo isso o monitoramento estará completo e respondendo!

<p align="center">
	<img src="src/images/Office365StatusZabbix1.15.png">
</p>
