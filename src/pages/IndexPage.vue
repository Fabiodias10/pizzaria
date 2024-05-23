<template>
  <q-page>
    <div class="q-pa-md row items-start q-gutter-md">
      <div v-for="item in pizza_json" :key="item.id">
        <q-card class="my-card" flat bordered>
          <q-card-section horizontal class="descricao_pizza">
            <q-card-section class="q-pt-xs">
              <!-- <div class="text-overline">Pizza salgada</div> -->
              <div class="text-h5 q-mt-sm q-mb-xs">{{ item.pizza }}</div>
              <div class="text-caption text-grey">
                {{ item.descricao }}
              </div>
            </q-card-section>
            <q-card-section class="col-5 flex flex-center">
              <q-img class="rounded-borders" :src="item.imagem" />
            </q-card-section>
          </q-card-section>
          <q-separator />
          <!-- <q-card-actions> -->

          <div class="row">
            <div class="col-8 flex flex-center">
              <q-btn flat color="primary"> R$ {{ item.valor }} </q-btn>
            </div>

            <div class="col-3">
              <div class="row teste">
                <div class="col">
                  <q-btn
                    color="green"
                    dense
                    glossy
                    icon="add"
                    @click="incrementar(item)"
                  ></q-btn>
                </div>
                <div class="col q-ml-md">
                  <q-input
                    borderless
                    v-model="item.quantidade_pizza"
                    type="number"
                  />
                </div>
                <div class="col">
                  <q-btn
                    color="red"
                    dense
                    glossy
                    icon="remove"
                    @click="decrementar(item)"
                  ></q-btn>
                </div>
              </div>
            </div>
          </div>

          <!-- </q-card-actions> -->
        </q-card>
      </div>
      <q-btn @click="alert = !alert" color="primary">botao carrinho</q-btn>
    </div>
    <ComponenteFilho :quantidadeTotal="totalQuantidade" />

    <q-dialog v-model="alert">
      <q-card>
        <q-card-section>
          <div class="text-h6">Pedido</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <div class="q-pa-xs">
            <q-markup-table flat>
              <thead>
                <tr>
                  <th class="text-left">Pizza</th>
                  <th class="text-right">Quantidade</th>
                  <th class="text-right">Valor</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="item in carrinho" :key="item.id">
                  <td class="text-left">{{ item.pizza }}</td>
                  <td class="text-center">{{ item.quantidade_pizza }}</td>
                  <td class="text-right">
                    {{ item.valor * item.quantidade_pizza }}
                  </td>
                </tr>
                <tr class="bg-indigo-3">
                  <td class="text-left text-overline">Total</td>
                  <td class="text-center text-overline">
                    {{ calculaTotalItemsCarrinho }}
                  </td>
                  <td class="text-right text-overline">
                    {{ calculaTotalCarrinho }}
                  </td>
                </tr>
              </tbody>
            </q-markup-table>
          </div>
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat label="Cancelar" v-close-popup />
          <q-btn flat label="Fazer pedido" color="primary" v-close-popup />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </q-page>
</template>

<script>
import { defineComponent } from "vue";
import ComponenteFilho from "../layouts/MainLayout.vue"; // Importa o componente filho
import pizzas from "../produtos/pizzas.js";

export default defineComponent({
  name: "IndexPage",
  components: {
    ComponenteFilho, // Registra o componente filho
  },
  data() {
    return {
      pizza_json: pizzas,
      quantidade_pizza: 0,
      resultados: [],
      carrinho: [],
      alert: false,
      total_teste: [],
    };
  },

  mounted() {
    console.log(pizzas);
  },
  computed: {
    totalQuantidade() {
      let total = 0;
      this.carrinho.forEach((item) => {
        total += item.quantidade_pizza;
      });
      return total;
    },
    calculaTotalCarrinho() {
      let total = 0;
      this.carrinho.forEach((item) => {
        // Multiplica o valor de cada item pela sua quantidade e adiciona ao total
        total += item.valor * item.quantidade_pizza;
      });
      return total;
    },
    calculaTotalItemsCarrinho() {
      let total = 0;
      this.carrinho.forEach((item) => {
        // Multiplica o valor de cada item pela sua quantidade e adiciona ao total
        total += item.quantidade_pizza;
      });
      return total;
    },
  },

  methods: {
    validarNumero(item) {
      if (item.quantidade_pizza < 0) {
        item.quantidade_pizza = 0;
      }
      console.log("validar numero");
    },
    incrementar(item) {
      item.quantidade_pizza++;
      this.adicionarAoCarrinho(item);
    },
    decrementar(item) {
      if (item.quantidade_pizza <= 0) {
        item.quantidade_pizza = 0;
      } else {
        item.quantidade_pizza--;
        this.removerDoCarrinho(item);
      }
    },
    adicionarAoCarrinho(item) {
      // Verifica se o item já está no carrinho
      const index = this.carrinho.findIndex(
        (carrinhoItem) => carrinhoItem.pizza === item.pizza
      );
      if (index === -1) {
        // Se não estiver no carrinho, adiciona
        this.carrinho.push({ ...item });
      } else {
        // Se estiver no carrinho, atualiza a quantidade
        this.carrinho[index].quantidade_pizza = item.quantidade_pizza;
      }
    },
    removerDoCarrinho(item) {
      // Procura o item no carrinho
      const index = this.carrinho.findIndex(
        (carrinhoItem) => carrinhoItem.pizza === item.pizza
      );

      // Verifica se o item está no carrinho
      if (index !== -1) {
        // Verifica se a quantidade é maior que 1
        if (this.carrinho[index].quantidade_pizza > 1) {
          // Se a quantidade for maior que 1, reduz apenas a quantidade
          this.carrinho[index].quantidade_pizza--;
        } else {
          // Se a quantidade for 1, remove o item completamente do carrinho
          this.carrinho.splice(index, 1);
        }
        this.$forceUpdate(); // Força a atualização do componente
      }
    },
  },
});
</script>

<style>
.my-card {
  width: 355px;
  height: 220px;
}

.valor_pizza {
  /* height: 1px; */
  /* border: 1px solid red; */
  /* background-color: silver; */
}

.descricao_pizza {
  height: 160px;
}

.q-input {
  width: 40px;
  text-align: center;
}

.teste {
  /* border: 1px solid black; */
  height: max-content;
  display: flex;
  justify-content: end;
  align-items: center;
}
</style>
