# calculadora-difal-sp
# Calculadora de DIFAL – São Paulo

Ferramenta web que calcula o DIFAL (diferencial de alíquota do ICMS) de notas fiscais eletrônicas (NF-e) de entrada interestaduais, com destino ao estado de São Paulo. Gera os valores para a guia DARE.

**Demo:** [https://gouveiaaa.github.io/calculadora-difal-sp/](https://gouveiaaa.github.io/calculadora-difal-sp/)

> Ferramenta de apoio. Os valores devem ser conferidos com um contador antes de qualquer pagamento.

---

## Funcionalidades

- **Validação do CNPJ** pelos dígitos verificadores.
- **Consulta cadastral** pela BrasilAPI, com razão social, situação cadastral e regime tributário.
- **Confirmação manual do regime** pelo CADESP, quando a consulta automática não estiver disponível.
- **Leitura de XMLs de NF-e** no navegador, sem envio para servidores.
- **Conferência do destinatário:** a nota só entra se o destinatário for o CNPJ informado.
- **Detecção de duplicatas** pela chave de acesso de 44 dígitos.
- **Filtros automáticos:** notas de fornecedores do mesmo estado, devoluções e eventos de cancelamento são recusados ou ignorados, com aviso.
- **Classificação por item** (uso e consumo, ativo imobilizado, industrialização, revenda), com sugestão pelo CFOP.
- **Cálculo por item e total**, com arredondamento em centavos.
- **Geração dos dados da guia DARE** para conferência e lançamento no portal da SEFAZ-SP.
- **Armazenamento local:** os dados ficam salvos no navegador de cada empresa, para continuar depois.

## Como usar

1. Digite o CNPJ da empresa e clique em **Confirmar CNPJ**.
2. Confira o regime. Empresas do Simples Nacional e MEI são liberadas automaticamente. Se a consulta falhar, confirme o regime no CADESP.
3. Envie um ou mais arquivos XML de NF-e de entrada.
4. Confira a classificação de cada item. O CFOP já sugere uma opção, mas você pode alterar.
5. Veja o DIFAL por item e o resumo por tipo.
6. Informe o nome da empresa e a competência e clique em **Gerar dados da guia DARE**.
7. Lance os valores no portal da SEFAZ-SP e confira o prazo de vencimento.

## Cálculo

Para cada item de uma nota interestadual com destino SP:

```
Base de cálculo (por dentro) = valor / (1 − 18%)
ICMS destino                 = base × 18%
ICMS origem                  = valor × alíquota interestadual
DIFAL                        = ICMS destino − ICMS origem   (mínimo zero)
```

- **Valor do item:** valor do produto, mais frete, seguro e outras despesas, menos desconto. Por padrão, o IPI não entra na base (`INCLUIR_IPI = false`).
- **Alíquota interestadual:** 12% para origens SP, MG, PR, RJ, RS e SC, e 7% para as demais UFs, conforme a tabela do Senado. Produtos importados (origem 1, 2, 3 e 8) usam 4%.
- **Alíquota interna de SP:** 18%.

Exemplo: nota de R$ 1.000,00 de MG, uso e consumo, sem importação.

```
Base = 1.000 / 0,82 = 1.219,51
ICMS destino = 1.219,51 × 0,18 = 219,51
ICMS origem  = 1.000 × 0,12 = 120,00
DIFAL = 99,51
```

## Configuração

As regras ficam no início do script, em constantes fáceis de alterar:

| Constante | Função |
|---|---|
| `ALIQ_UF` | Alíquota interestadual por UF de origem |
| `IMPORTADO` | Origens de produto importado (alíquota de 4%) |
| `TIPOS_DIFAL` | Tipos de classificação que geram DIFAL |
| `CFOP_TIPO` | CFOP que sugere a classificação de cada item |
| `INCLUIR_IPI` | Inclui ou não o IPI na base |
| `MAX_BYTES` | Tamanho máximo de cada XML |

## Limitações conhecidas

- **ICMS-ST não é calculado.** Itens com substituição tributária geram um aviso para conferência manual.
- **Vencimento fixo.** A data sugerida é o último dia do mês seguinte à geração. Confira o prazo no portal da SEFAZ-SP.
- **Tabelas dependem de conferência.** As alíquotas e os CFOPs precisam ser validados com fonte oficial e com um contador. As regras de DIFAL mudaram nos últimos anos e podem mudar de novo.
- **Verificação de regime é orientativa.** A checagem roda no navegador, então pode ser contornada. Ela serve como orientação para o usuário, não como controle de acesso.
- **Dados locais.** As informações ficam no `localStorage` do navegador. Quem usa computadores compartilhados deve apagar os dados ao final, pelo botão **Apagar dados salvos desta empresa**.

## Privacidade

- Os XMLs são lidos no navegador e não são enviados a nenhum servidor deste projeto.
- O CNPJ é enviado à BrasilAPI para a consulta cadastral.
- Não há cookies, cadastro de usuário nem analytics.

## Tecnologias

HTML, CSS e JavaScript puros, sem frameworks nem dependências externas. A consulta cadastral usa a API pública da BrasilAPI.

## Como rodar localmente

Não é preciso instalar nada. Basta abrir o arquivo `index.html` no navegador.

Para testar com um servidor local:

```bash
python3 -m http.server 8000
```

Depois acesse `http://localhost:8000`.

## Testes

Em andamento. A pasta `tests/` vai reunir notas fiscais anonimizadas e os resultados esperados, calculados à mão, para validar o parser e o cálculo.

## Roadmap

- [ ] Testes automatizados com NF-es anonimizadas
- [ ] Tratamento de ICMS-ST
- [ ] Troca de `alert()` e `confirm()` por mensagens na própria página
- [ ] Vencimento configurável

## Autor

**Pedro Augusto Gouveia Rodrigues**
Estudante de Sistemas de Informação

- LinkedIn: [linkedin.com/in/pedro-augusto-gouveia-rodrigues](https://www.linkedin.com/in/pedro-augusto-gouveia-rodrigues)
- GitHub: [github.com/Gouveiaaa](https://github.com/Gouveiaaa)

## Licença

Distribuído sob a licença MIT. Veja o arquivo [LICENSE](LICENSE)
