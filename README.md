# Reservas de Hoteis

**Dados**

**Aluno:** Gabriel da Silva oliveira Costa.

**RA:** 124113980.

**Professor:** Marcos.

# Resumo

Este projeto tem como obejetivo construir um sistema que atenda demandas de agendamento reservas de hoteis.

# Descrição do problema


O problema previsto são as colisões que são encontradas no decorrer da incersão de dados, no caso de reservas para a mesma data. Para tratar essas colisões da hash foi criado uma fila, a onde, o usuario que deseja agendar uma data que já esteja ocupada, ela entre em uma "Fila de espera", até que ocorra o cancelamento do outro usario. Caso ocorra o cancelamento de uma data, o usuario que estava na fila de espera tomará o lugar automaticamente. 

Para que seja demonstrado a funcionalidade do sistema, foi implementado um carga de arquivos de resevas, para demonstrar o tratamento das possiveis colisões. 
O projeto foi criado na linguagem JAVA.

# Função HASH

        private int hashSimples(String entrada) {
            int valorHash = 0;
            for (char c : entrada.toCharArray()) {
                valorHash = (valorHash * 31 + (int) c) % 1_000_000_007;
            }
            return valorHash;
        }

Função HASH que recebe uma string como entrada e retorna um valor hash. O uso do número primo 31 como multiplicador ajuda a distribuir melhor os valores de hash e reduzir colisões.

