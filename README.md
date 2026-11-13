# Compre_Facil
Repositório dedicado ao projeto de Gestão Ágil, ministrado pelo docente Alexandre, realizado no segundo semestre de 2026. Sistema de navegação inteligente integrado a carrinhos de supermercado que utiliza localização interna para auxiliar os usuários durante suas compras.


## 📋 Sobre o Projeto

O **Compre Fácil** oferece uma interface acoplada ao próprio carrinho de compras, permitindo que o cliente pesquise produtos e receba orientações até a sua localização exata dentro do estabelecimento. 

Desenvolvida com foco em **acessibilidade e autonomia**, a solução conta com:
* Comandos e orientações por voz.
* Interface intuitiva e linguagem simplificada para pessoas com deficiência.
* Cálculo de rotas de menor deslocamento, adequadas para pessoas com mobilidade reduzida, garantindo uma experiência de compra mais eficiente e inclusiva.


## ⚙️ Requisitos do Sistema

### Requisitos Funcionais (RF)
* Permitir que o usuário pesquise produtos disponíveis no supermercado.
* Informar a localização do produto pesquisado e apresentar uma rota até o seu local.
* Consultar o banco de dados do supermercado para verificar a disponibilidade dos produtos em estoque.
* Fornecer instruções de navegação por meio de comandos e orientações de voz.
* Disponibilizar uma interface com elementos visuais e linguagem simplificados, visando facilitar sua utilização por pessoas com diferentes necessidades de acessibilidade.
* Recalcular ou atualizar a rota quando necessário, considerando alterações na localização do usuário ou indisponibilidade de determinado produto.
* Exibir um aviso quando os produtos pesquisados não estiverem disponíveis.
* Permitir novas consultas de rotas para produtos após uma consulta já ter sido iniciada.
* Oferecer recomendações de produtos semelhantes ao usuário em caso de indisponibilidade do item buscado.

### Requisitos Não Funcionais (RNF)
* Possuir interface simples, intuitiva e de fácil utilização.
* Atender às necessidades de acessibilidade, oferecendo recursos de interação por voz e linguagem simplificada.
* Garantir a confidencialidade, integridade e disponibilidade das informações processadas.
* Permitir integração com os sistemas e banco de dados utilizados pelo supermercado.
* Fornecer informações de localização e disponibilidade de produtos de forma consistente, reduzindo a possibilidade de orientações incorretas.


## 🔄 Fluxos de Uso

### 1. Fluxo Geral
1. **Início:** Exibição da tela inicial.
2. **Seleção de Ação:** O usuário escolhe entre:
   * `(A)` Pesquisar produto
   * `(B)` Lista de Compras
   * `(C)` Ofertas do Dia
   * `(D)` Chamar atendente
   * `(E)` Botões de configuração
3. **Retorno `(G)`:** Após concluir qualquer subfluxo, o sistema pergunta se o usuário deseja *Continuar navegando?*. Se **Sim**, retorna à tela inicial; se **Não**, encerra o fluxo.

### 2. Busca de Produtos `(A)` e Rastreamento `(R)`
1. Tocar no campo de pesquisa e digitar o termo do produto.
2. Visualizar sugestões de busca e tocar em **Encontrar** ou **Buscar**.
3. Nos resultados da busca, o usuário pode:
   * **Adicionar produto** diretamente à lista/carrinho `(-> G)`.
   * **Abrir detalhes** para visualizar informações e decidir entre *Adicionar produto* `(-> G)` ou *Rastrear agora* `(-> R)`.
   * **Rastrear** diretamente `(-> R)`.
4. **Subfluxo de Rota `(R)`:** Exibe a rota até o produto. O usuário pode optar por *Seguir instruções da rota* ou *Cancelar rota* (encerrando-a). Ambos retornam ao fluxo principal `(-> G)`.

### 3. Lista de Compras `(B)`
* **Criar Lista:**
  1. Buscar produto por nome, categoria ou sugestão e tocar em `+` no item desejado.
  2. Atualizar **Meu Carrinho** (itens, quantidade, total).
  3. Decidir se deseja adicionar mais produtos (loop de busca) ou alterar itens do carrinho (alterar quantidade ou remover item).
  4. Visualizar o carrinho (`Ver Carrinho`) e retornar `(-> G)`.
* **Importar Lista:**
  1. Escolher o método de importação: **Ler QR Code** ou **Digitar código** (e clicar em *Adicionar Lista*).
  2. Importar a lista do aplicativo e retornar `(-> G)`.

### 4. Aba de Ofertas `(C)`
* Exibe as ofertas do dia com opção de filtrar ou navegar pelos itens.
* Ações disponíveis: **Adicionar produto** `(-> G)` ou **Rastrear agora** `(-> R)`.

### 5. Solicitação de Auxílio Humano `(D)`
* Exibe a tela *"Atendente a caminho"*.
* O usuário pode optar por **Aguardar atendente** ou tocar em **Voltar** `(-> G)`.

### 6. Configurações de Acessibilidade `(E)`
Permite ajustar quatro parâmetros principais antes de clicar em **Aplicar ajuste** `(-> G)`:
* **Volume:** Controle deslizante.
* **Contraste:** 3 modos (Padrão, Alto Contraste e Modo Escuro).
* **Brilho:** Controle deslizante.
* **Texto:** Níveis e slider.


## 🏗️ Arquitetura Interna

O funcionamento do **Compre Fácil** é composto por três subsistemas integrados: captura/reconhecimento visual via câmeras, validação/persistência da localização oficial em banco de dados e exibição de informações/rotas na interface do carrinho.

### Visão Geral dos Componentes

| Etapa | Componente | Função no Sistema | Responsável / Tecnologia |
| :--- | :--- | :--- | :--- |
| **1** | Câmera | Captura contínua da imagem da prateleira. | Hardware (Webcam ou Câmera IP) |
| **2** | OpenCV | Captura o vídeo e separa em imagens (*frames*). | Roboflow / Python |
| **3** | Roboflow | Anotação de metadados (etiquetas) e *bounding boxes* nas imagens. | Python (IA) |
| **4** | YOLO | Leitura do vídeo e divisão em *frames* individuais para detecção. | Python |
| **5** | Módulo de Zonas | Converte a posição na imagem em corredor, prateleira e zona. | Python |
| **6** | Confirmação | Valida a mudança somente se a nova posição ficar estável. | Python (Lógica) |
| **7** | API (FastAPI) | Recepção dos dados da IA e gravação no banco de dados. | Integração (Python) |
| **8** | Banco de Dados | Armazenamento relacional de produtos, corredores, prateleiras e zonas. | PostgreSQL (SQL) |
| **9** | Sistema do Carrinho | Lê a localização atualizada e exibe ao usuário. | HTML, CSS e JavaScript |

### Detalhamento Técnico do Pipeline

* **Captura de Imagens e Cuidados Operacionais:** A câmera aponta fixamente para 2 ou 3 prateleiras em monitoramento contínuo. Ela deve permanecer imóvel e bem iluminada, pois qualquer deslocamento desalinha as zonas virtuais.
* **Reconhecimento com YOLO (You Only Look Once):** 
  * *Preparação:* 5 a 10 produtos são fotografados em diferentes posições e iluminações, etiquetados no **Roboflow** com *bounding boxes* e metadados para treinar o modelo YOLO.
  * *Saída por frame:* Nome do produto, caixa delimitadora (*bounding box*) com posição na imagem e nível de confiança (ex.: `Arroz 92%` — detecções com baixa confiança são ignoradas).
* **Mapeamento de Zonas:** A prateleira é dividida em zonas virtuais (`A`, `B` e `C`), dispensando medições em centímetros. O módulo calcula o centro da *bounding box* gerada pelo YOLO e identifica em qual zona ele está inserido, associando-a a um endereço físico fixo pré-configurado (corredor, prateleira e zona).
* **Validação das Posições (Regra de Estabilidade):** Para evitar atualizações errôneas causadas por clientes que deixam itens fora do lugar momentaneamente, o banco mantém a última zona confirmada. A atualização só é enviada à API se o YOLO detectar o produto em uma nova zona durante vários *frames* seguidos (ex.: 3 a 5 segundos). Caso o produto desapareça da imagem, nenhuma alteração é feita.
* **Integração via API:** As equipes da IA/API e do carrinho operam de forma desacoplada. As câmeras escrevem dados via endpoint REST e o carrinho apenas consome as leituras finais:
