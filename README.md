# JavaReflection

Trabalho acadêmico desenvolvido em 2023 para a disciplina de **Tópicos Avançados em Tecnologia**, do curso de **Ciência da Computação**.

O projeto implementa um pequeno framework de serialização de objetos Java para XML usando **Reflection** e **anotações customizadas**. As classes de domínio são marcadas com a anotação `@XmlElement`, e a classe `XmlUtils` percorre seus campos em tempo de execução para montar o XML correspondente, sem nenhuma biblioteca externa.

## Conceitos abordados

- **Reflection** (`java.lang.reflect`): leitura de campos privados com `Field`, `getDeclaredFields()` e `setAccessible()`.
- **Anotações em tempo de execução**: anotação própria com `@Retention(RUNTIME)` aplicável a classes e campos.
- **Padrão Strategy**: cada tipo de campo (objeto anotado, `List`, `Map`, `Date`) tem uma estratégia própria de geração de XML.
- **Static factory methods** (`of(...)`) com validação de argumentos.

## Estrutura

```
src/main/java
├── main/App.java                         # Exemplo de uso
└── br/edu/fema
    ├── domain/model                      # Classes de domínio anotadas
    │   ├── Aluno.java
    │   ├── Curso.java
    │   ├── Disciplina.java
    │   └── Professor.java
    └── framework
        ├── annotations/XmlElement.java   # Anotação de mapeamento
        └── utils
            ├── XmlUtils.java             # Ponto de entrada: toXml(Object)
            ├── XmlGenerationStrategy.java
            ├── ElementGenerator.java     # Objetos anotados com @XmlElement
            ├── ListGenerator.java        # Listas
            ├── MapGenerator.java         # Mapas
            └── DateGenerator.java        # Datas formatadas
```

## A anotação `@XmlElement`

| Atributo         | Descrição                                                                  |
| ---------------- | -------------------------------------------------------------------------- |
| `value`          | Nome da tag no XML. Se vazio, usa o nome da classe ou do campo.            |
| `enabled`        | Permite desligar a serialização de uma classe ou campo (padrão `true`).    |
| `fieldAttribute` | Em `Map`s, campo da chave usado como atributo da tag (ex.: `descricao`).   |
| `itemsName`      | Em `Map`s cujo valor é uma lista, nome da tag de cada item (ex.: `nota`).  |
| `dateFormat`     | Padrão de formatação para campos `Date` (ex.: `dd/MM/yyyy`).               |

Exemplo:

```java
@XmlElement("aluno")
public class Aluno {
    @XmlElement
    private String nome;

    @XmlElement(fieldAttribute = "descricao", itemsName = "nota")
    private Map<Disciplina, List<BigDecimal>> notas;

    @XmlElement(dateFormat = "dd/MM/yyyy")
    private Date dataMatricula;
}
```

```java
String xml = XmlUtils.toXml(aluno);
```

## Como executar

Requisitos: **Java 11+** e **Maven**.

```bash
mvn compile
```

```bash
java -cp target/classes main.App
```

A classe `App` cria um aluno com curso, disciplinas, professores e notas, calcula as médias e imprime o XML gerado, algo como:

```xml
<aluno><ra>100</ra><nome>João</nome><curso><sigla>BCC</sigla>...</curso>...<notas><disciplina descricao="Tópicos Avançados"><nota>7</nota>...</disciplina>...</notas>...</aluno>
```

## Uso de inteligência artificial

Todo o código-fonte deste projeto foi escrito por mim, em 2023, sem o uso de inteligência artificial.

Em 2026, a IA **Claude** (Anthropic) foi utilizada apenas para:

- escrever este `README.md`;
- remover do repositório arquivos desnecessários à arquitetura do projeto (saída de compilação em `target/` e configurações de IDE em `.idea/`, `.settings/`, `.classpath` e `.project`) e criar o `.gitignore` correspondente.

## Licença

Distribuído sob a licença MIT. Veja [LICENSE](LICENSE).
