# Wait-On

[![Python Tests](https://github.com/keviocastro/wait-on/actions/workflows/python-tests.yml/badge.svg)](https://github.com/keviocastro/wait-on/actions/workflows/python-tests.yml)

Uma implementação em Python do utilitário wait-on, inspirada na versão Node.js [wait-on](https://github.com/jeffbski/wait-on).

## Descrição

Python Wait-On é um utilitário de linha de comando multiplataforma que aguarda arquivos, portas, sockets e recursos HTTP(S) ficarem disponíveis (ou não disponíveis usando o modo reverso). É útil para sincronizar tarefas em scripts, como esperar que um servidor esteja pronto antes de iniciar testes ou esperar que um arquivo seja criado antes de processá-lo.

## Instalação

```bash
# Instalar do PyPI
pip install python-wait-on

# Ou instalar a partir do código fonte
git clone https://github.com/yourusername/python-wait-on.git
cd python-wait-on
pip install -e .
```

## Uso via Linha de Comando

```bash
# Aguardar por um arquivo e então executar um comando
python-wait-on arquivo1.txt && echo "Arquivo disponível!"

# Aguardar por múltiplos arquivos
python-wait-on arquivo1.txt arquivo2.txt && echo "Arquivos disponíveis!"

# Aguardar por um endpoint HTTP
python-wait-on http://localhost:8000/api && echo "API disponível!"

# Aguardar por um endpoint HTTPS
python-wait-on https://meuservidor.com/api && echo "API disponível!"

# Aguardar por um endpoint HTTP usando GET em vez de HEAD
python-wait-on http-get://localhost:8000/api && echo "API disponível!"

# Aguardar por uma porta TCP
python-wait-on tcp:localhost:4000 && echo "Porta disponível!"

# Aguardar por um socket de domínio
python-wait-on socket:/tmp/meusocket.sock && echo "Socket disponível!"

# Modo reverso: aguardar até que um recurso NÃO esteja disponível
python-wait-on -r http://localhost:8000/api && echo "API não está mais disponível!"

# Definir timeout de 30 segundos
python-wait-on -t 30000 http://localhost:8000/api && echo "API disponível!"

# Verificar a cada 1 segundo
python-wait-on -i 1000 http://localhost:8000/api && echo "API disponível!"

# Atraso inicial de 5 segundos antes de começar a verificar
python-wait-on -d 5000 http://localhost:8000/api && echo "API disponível!"

# Janela de estabilidade de 2 segundos
python-wait-on -w 2000 arquivo1.txt && echo "Arquivo estável por 2 segundos!"

# Modo verboso
python-wait-on -v http://localhost:8000/api && echo "API disponível!"
```

## Uso via API Python

Você também pode usar o Python Wait-On diretamente em seu código Python:

```python
from python_wait_on.wait_on import wait_on

# Aguardar por um arquivo
result = wait_on(['arquivo.txt'])

# Aguardar por múltiplos recursos com opções personalizadas
result = wait_on(
    resources=['http://localhost:8000', 'tcp:localhost:5000'],
    delay=1000,       # Atraso inicial de 1 segundo
    interval=500,     # Verificar a cada 0.5 segundos
    timeout=30000,    # Timeout de 30 segundos
    reverse=False,    # Modo normal (não reverso)
    window=1000,      # Janela de estabilidade de 1 segundo
    verbose=True      # Modo verboso
)

if result:
    print("Todos os recursos estão disponíveis!")
else:
    print("Timeout ou interrupção!")
```

## Tipos de Recursos Suportados

- **Arquivo**: Caminho para um arquivo local (padrão se nenhum prefixo for especificado)
  - Exemplo: `arquivo.txt` ou `file:arquivo.txt`

- **HTTP/HTTPS (HEAD)**: URL HTTP ou HTTPS que será verificada usando o método HEAD
  - Exemplo: `http://localhost:8000` ou `https://exemplo.com/api`

- **HTTP/HTTPS (GET)**: URL HTTP ou HTTPS que será verificada usando o método GET
  - Exemplo: `http-get://localhost:8000` ou `https-get://exemplo.com/api`

- **TCP**: Porta TCP que será verificada se está aberta
  - Exemplo: `tcp:localhost:8000` ou `tcp:8000` (assume localhost)

- **Socket**: Socket de domínio Unix que será verificado
  - Exemplo: `socket:/tmp/meusocket.sock`

## Opções

- `-d, --delay`: Atraso inicial antes de verificar os recursos em ms, padrão 0
- `-i, --interval`: Intervalo para verificar os recursos em ms, padrão 250ms
- `-r, --reverse`: Operação reversa, aguardar até que os recursos NÃO estejam disponíveis
- `-t, --timeout`: Tempo máximo em ms para aguardar antes de sair com código de falha (1), padrão Infinito
- `-w, --window`: Janela de estabilidade, o tempo em ms definindo a janela de tempo que o recurso precisa não ter mudado (tamanho do arquivo ou disponibilidade) antes de sinalizar sucesso, padrão 750ms
- `-v, --verbose`: Exibir informações detalhadas durante a execução
- `-h, --help`: Exibir ajuda

## Códigos de Saída

- `0`: Todos os recursos estão disponíveis (ou indisponíveis no modo reverso)
- `1`: Timeout ou erro ao verificar recursos

## Exemplos de Uso em Cenários Reais

### Aguardar por um servidor web antes de executar testes

```bash
# Iniciar o servidor em segundo plano
python servidor.py &

# Aguardar até que o servidor esteja pronto e então executar os testes
python-wait-on -t 60000 http://localhost:8000 && python -m pytest
```

### Aguardar por um arquivo gerado por outro processo

```bash
# Iniciar processo que gera um arquivo
python gerador.py &

# Aguardar até que o arquivo seja gerado e esteja estável
python-wait-on -w 2000 arquivo_gerado.txt && python processar.py
```

### Aguardar até que um serviço seja encerrado

```bash
# Enviar sinal para encerrar o serviço
kill $PID_DO_SERVICO

# Aguardar até que a porta não esteja mais em uso
python-wait-on -r tcp:8000 && echo "Serviço encerrado com sucesso!"
```

## Licença

MIT
