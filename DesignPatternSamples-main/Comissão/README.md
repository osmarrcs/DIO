# DesignPatternSamples
|Branch|Build|
|-:|-|
|Develop|![.NET Core](https://github.com/fructuoso/DesignPatternSamples/workflows/.NET%20Core/badge.svg?branch=develop)|
|Main|![.NET Core](https://github.com/fructuoso/DesignPatternSamples/workflows/.NET%20Core/badge.svg?branch=main)|

Aplicação de exemplo de aplicação de Design Patterns na prática em um projeto WebAPI .NET Core 3.1 utilizada na palestra "Aplicando design patterns na prática com C#" ([Link Apresentação](Apresenta%C3%A7%C3%A3o/Aplicando%20design%20patterns%20na%20pr%C3%A1tica%20com%20C%23.pdf))

## Comissão
Passo a passo sobre como executar os testes unitários (e calcular o code coverage) localmente antes de realizar o commit.

<u>Obs.: O VS2019 possui esta funcionalidade nativamente, porém ela só está habilitada para a versão Enterprise segundo a [documentação](https://docs.microsoft.com/pt-br/visualstudio/test/using-code-coverage-to-determine-how-much-code-is-being-tested?view=vs-2019) da própria Microsoft.</u>

### Pré-Requisitos

Para gerar o relatório é necessário instalar o **dotnet-reportgenerator-globaltool**

```script
dotnet tool install --global dotnet-reportgenerator-globaltool --version 4.6.1
````

## Padrões na Prática

### Strategy

#### Problema:

Nosso objetivo é Utilizar o método Distinct do System.Linq, este por sua vez espera como entrada uma IEqualityComparer. Isso por si só já representa uma implementação de Strategy, entretanto nós não queremos criar uma única implementação engessada que nos permita comparar um determinado objeto de uma única forma.

##### Solução:

1. Criar uma classe que implemente a interface [IEqualityComparer](https://docs.microsoft.com/pt-br/dotnet/api/system.collections.generic.iequalitycomparer-1?view=netcore-3.1);
2. Esta classe deve receber o 'como' os objetos deverão ser comparados através de um parâmetro, que neste caso é uma função anônima;

Desta forma a classe que criamos sabe comparar objetos, porém ela não sabe os critérios que serão utilizados, os critérios serão injetados através de uma função anônima.

[Implementação](src/Workbench.Comparer/GenericComparerFactory.cs)\
[Consumo](src/Workbench.GenericComparer.Tests/GenericComparerFactoryTest.cs#L27)

Podemos tornar o consumo ainda mais interessante criando uma *Sugar Syntax* através de métodos de extensão.

[Implementação](src/Workbench.Linq.Extensions/DistinctExtensions.cs)\
[Consumo](src/Workbench.Linq.Extensions.Tests/DistinctExtensionsTests.cs#L26)

Desta forma através do padrão [Strategy](#strategy) estamos aderentes ao princípio **Aberto-Fechado** e **Inversão de Controle**.

### Factory

#### Problema: 

Nós queremos criar um serviço de consulta de débitos do veículo que seja capaz de acessar o sistema do DETRAN e obter estas informações, porém o DETRAN possui uma aplicação completamente diferente de acordo com o estado.

Nós não queremos modificar o nosso serviço sempre que um novo estado for implementado.

#### Solução:

1. Criar uma interface que determine uma assinatura única para o serviço;
2. Realizar uma implementação para cada um dos estados;
3. Criar uma classe Factory, onde sua responsabilidade será determinar qual classe concreta deverá ser instanciada;

Desta forma através do padrão [Factory](#factory) estamos aderentes ao princípio **Aberto-Fechado**.

Neste exemplo o nosso [Factory](#factory) ainda está diretamente relacionado ao princípio **Substituição de Liskov**.

### Template Method

#### Problema:

Visto que em algum momento iremos implementar 27 serviços diferentes para acessar os DETRAN que temos espalhados pelo Brasil.

Entendemos que, mesmo que, os sites sejam diferentes, os passos necessários para extrair a informação costumam ser semelhantes:

* Consultar Comissões

