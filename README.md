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

# Codigo

        import java.util.HashMap;
import java.util.LinkedList;
import java.util.List;
import java.util.Map;

public class SistemaReservasHotel {
    private Map<Integer, List<Map<String, String>>> reservas;
    private Map<Integer, List<Map<String, String>>> listaEspera;

    public SistemaReservasHotel() {
        reservas = new HashMap<>();
        listaEspera = new HashMap<>();
    }

    private int hashSimples(String entrada) {
        int valorHash = 0;
        for (char c : entrada.toCharArray()) {
            valorHash = (valorHash * 31 + (int) c) % 1_000_000_007;
        }
        return valorHash;
    }

    public void adicionarReserva(String data, Map<String, String> detalhesReserva) {
        int hashData = hashSimples(data);
        if (!reservas.containsKey(hashData)) {
            reservas.put(hashData, new LinkedList<>());
        }
        if (dataDisponivel(hashData)) {
            reservas.get(hashData).add(detalhesReserva);
            System.out.println("Reserva adicionada para " + detalhesReserva.get("nome") + " no quarto " + detalhesReserva.get("numero_quarto") + " na data " + data + ".");
        } else {
            listaEspera.putIfAbsent(hashData, new LinkedList<>());
            listaEspera.get(hashData).add(detalhesReserva);
            System.out.println("Data ocupada. Reserva adicionada à lista de espera para " + detalhesReserva.get("nome") + " no quarto " + detalhesReserva.get("numero_quarto") + " na data " + data + ".");
        }
    }

    public List<Map<String, String>> obterReservas(String data) {
        int hashData = hashSimples(data);
        return reservas.getOrDefault(hashData, new LinkedList<>());
    }

    public void removerReserva(String data, Map<String, String> detalhesReserva) {
        int hashData = hashSimples(data);
        if (reservas.containsKey(hashData)) {
            if (reservas.get(hashData).remove(detalhesReserva)) {
                System.out.println("Reserva removida para " + detalhesReserva.get("nome") + " no quarto " + detalhesReserva.get("numero_quarto") + " na data " + data + ".");
                if (reservas.get(hashData).isEmpty()) {
                    reservas.remove(hashData);
                }
                if (listaEspera.containsKey(hashData) && !listaEspera.get(hashData).isEmpty()) {
                    Map<String, String> proximoDaFila = listaEspera.get(hashData).remove(0);
                    reservas.get(hashData).add(proximoDaFila);
                    System.out.println("Reserva da lista de espera movida para ativa: " + proximoDaFila.get("nome") + " no quarto " + proximoDaFila.get("numero_quarto") + " na data " + data + ".");
                    if (listaEspera.get(hashData).isEmpty()) {
                        listaEspera.remove(hashData);
                    }
                }
            } else {
                System.out.println("Reserva não encontrada.");
            }
        } else {
            System.out.println("Data não encontrada nas reservas.");
        }
    }

    private boolean dataDisponivel(int hashData) {
        return !reservas.containsKey(hashData) || reservas.get(hashData).isEmpty();
    }

    public static void main(String[] args) {
        SistemaReservasHotel sistemaHotel = new SistemaReservasHotel();
        java.util.Scanner scanner = new java.util.Scanner(System.in);

        while (true) {
            System.out.println("\n--- Sistema de Reservas de Hotel ---");
            System.out.println("1. Adicionar reserva");
            System.out.println("2. Consultar reservas");
            System.out.println("3. Remover reserva");
            System.out.println("4. Sair");

            System.out.print("Escolha uma opção: ");
            String escolha = scanner.nextLine();

            switch (escolha) {
                case "1":
                    System.out.print("Digite a data da reserva (AAAA-MM-DD): ");
                    String data = scanner.nextLine();
                    System.out.print("Digite o nome do hóspede: ");
                    String nome = scanner.nextLine();
                    System.out.print("Digite o número do quarto: ");
                    String numeroQuarto = scanner.nextLine();
                    Map<String, String> detalhesReserva = new HashMap<>();
                    detalhesReserva.put("nome", nome);
                    detalhesReserva.put("numero_quarto", numeroQuarto);
                    sistemaHotel.adicionarReserva(data, detalhesReserva);
                    break;

                case "2":
                    System.out.print("Digite a data para consultar reservas (AAAA-MM-DD): ");
                    data = scanner.nextLine();
                    List<Map<String, String>> reservas = sistemaHotel.obterReservas(data);
                    if (!reservas.isEmpty()) {
                        System.out.println("Reservas para a data " + data + ":");
                        for (Map<String, String> res : reservas) {
                            System.out.println("- Hóspede: " + res.get("nome") + ", Quarto: " + res.get("numero_quarto"));
                        }
                    } else {
                        System.out.println("Não há reservas para a data " + data + ".");
                    }
                    break;

                case "3":
                    System.out.print("Digite a data da reserva a ser removida (AAAA-MM-DD): ");
                    data = scanner.nextLine();
                    System.out.print("Digite o nome do hóspede da reserva a ser removida: ");
                    nome = scanner.nextLine();
                    System.out.print("Digite o número do quarto da reserva a ser removida: ");
                    numeroQuarto = scanner.nextLine();
                    detalhesReserva = new HashMap<>();
                    detalhesReserva.put("nome", nome);
                    detalhesReserva.put("numero_quarto", numeroQuarto);
                    sistemaHotel.removerReserva(data, detalhesReserva);
                    break;

                case "4":
                    System.out.println("Saindo do sistema de reservas.");
                    scanner.close();
                    return;

                default:
                    System.out.println("Opção inválida. Tente novamente.");
            }
        }
    }
}


