# FleetCheck – Build Systems Lab

This project is intentionally incomplete. Follow the worksheet in the order given.

Expected final application output:

```
FleetCheck 1.0
Vehicles loaded: 4
Vehicles requiring service: 2
Average mileage: 37000 km
```

Do not copy the solution POM. The objective is to observe how each build change alters the result.

# Relatório - Build Systems (FleetCheck)

## Passo 1 - Evidência 1

### Linha do [ERROR] observada:
```package com.fasterxml.jackson.core.type does not exist```

## Passo 2 - Pergunta

Esta falha é melhor porque indica que a fase de compilação foi concluída com sucesso e a pipeline de construção progrediu.
Enquanto a falha do Passo 1 era um erro de compilação (dependência do Jackson em falta), a falha do Passo 2 ocorre na fase de testes (`test` / `maven-surefire-plugin`). Isto significa que o código é sintaticamente válido e compilável, e o sistema de build está agora a cumprir o seu papel de controlo de qualidade ao detetar um defeito comportamental (bug funcional) antes de permitir a criação do pacote final.

## Passo 4 - Evidence 4

O JAR padrão criado pelo Maven contém apenas o código-fonte compilado da própria aplicação. Ele não inclui as bibliotecas externas necessárias para a execução (como o Jackson) nem define a classe principal no ficheiro de manifesto (`META-INF/MANIFEST.MF`), tornando-o impossível de executar diretamente com `java -jar`.
O Maven Shade Plugin altera este comportamento ao criar um *Fat JAR* (`fleetcheck-1.0.0-all.jar`). Ele empacota todo o código do projeto juntamente com os ficheiros `.class` de todas as suas dependências num único ficheiro `.jar` e configura a propriedade `Main-Class` apontando para `pt.upt.fleetcheck.App`. Isto torna o artefato final completamente autónomo e pronto a ser executado em qualquer ambiente sem necessidade de dependências externas adicionais.

## Passo 5 - Pergunta

O Maven Wrapper removeu a suposição de que a máquina do desenvolvedor ou o servidor de integração contínua (CI/CD) possui uma instalação do Maven previamente configurada nas variáveis de ambiente do sistema.
Ao incluir os scripts `mvnw` / `mvnw.cmd` e a pasta `.mvn/wrapper/`, o próprio projeto passa a controlar a versão exata do Maven necessária para a sua construção. Isto garante que qualquer pessoa ou sistema automatizado consiga executar exatamente o mesmo processo de build com a mesma versão do Maven, sem necessidade de instalações manuais prévias no sistema operativo.

## Passo 7 - Evidence 7

O SBOM (`bom.json`) inclui componentes adicionais porque o plugin CycloneDX realiza uma análise do grafo completo de dependências do Maven, registando tanto as dependências diretas como as dependências transitivas.

Embora apenas tenhamos declarado diretamente no `pom.xml`, estas bibliotecas necessitam de outros módulos para funcionar. Para garantir a segurança da cadeia de suprimentos de software, o SBOM deve mapear 100% dos componentes e bibliotecas de terceiros realmente presentes no projeto final e no ambiente de testes.