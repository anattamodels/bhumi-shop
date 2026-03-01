<template>
  <div class="config-page container">
    <h1 class="page-title">Configurações</h1>

    <div class="config-sections">
      <section class="config-section">
        <h2>Informações da Loja</h2>
        <form @submit.prevent="saveConfig">
          <div class="form-group">
            <label>Nome da Loja</label>
            <input v-model="config.storeName" type="text" placeholder="BHUMI SHOP">
          </div>

          <div class="form-group">
            <label>Descrição</label>
            <textarea v-model="config.storeDescription" rows="3" placeholder="Arte, Conhecimento e Criatividade"></textarea>
          </div>

          <div class="form-group">
            <label>Email de Contato</label>
            <input v-model="config.contactEmail" type="email" placeholder="contato@bhumi.com.br">
          </div>
        </form>
      </section>

      <section class="config-section">
        <h2>Configurações de Pagamento</h2>
        
        <div class="form-group">
          <label>Chave PIX</label>
          <input v-model="config.pixKey" type="text" placeholder="sua-chave-pix@email.com">
          <small>Sua chave PIX para recebimento</small>
        </div>

        <div class="form-group">
          <label>WhatsApp (para pedidos)</label>
          <input v-model="config.whatsapp" type="text" placeholder="5511999999999">
          <small>Número com DDI (ex: 55 para Brasil)</small>
        </div>

        <div class="form-group">
          <label>Email PayPal</label>
          <input v-model="config.paypalEmail" type="email" placeholder="seu-email@paypal.com">
        </div>

        <div class="form-group">
          <label>Access Token Mercado Pago</label>
          <input v-model="config.mercadopagoToken" type="password" placeholder="APP_USR-...">
          <small>Token de acesso do Mercado Pago (ambiente sandbox/produção)</small>
        </div>
      </section>

      <section class="config-section">
        <h2>Configurações de Envio</h2>
        <form @submit.prevent="saveConfig">
          <div class="form-group">
            <label>Frete Grátis a partir de (R$)</label>
            <input v-model.number="config.freeShippingAbove" type="number" step="0.01" placeholder="199.00">
          </div>

          <div class="form-group">
            <label>Valor do Frete Padrão (R$)</label>
            <input v-model.number="config.defaultShipping" type="number" step="0.01" placeholder="15.00">
          </div>

          <div class="form-group">
            <label>Prazo de Produção (dias)</label>
            <input v-model.number="config.productionDays" type="number" placeholder="5">
          </div>
        </form>
      </section>

      <section class="config-section">
        <h2>Políticas da Loja</h2>
        <form @submit.prevent="saveConfig">
          <div class="form-group">
            <label>Política de Troca</label>
            <textarea v-model="config.returnPolicy" rows="3" placeholder="Descrição da política de troca..."></textarea>
          </div>

          <div class="form-group">
            <label>Informações de Envio</label>
            <textarea v-model="config.shippingInfo" rows="3" placeholder="Informações sobre envio..."></textarea>
          </div>
        </form>
      </section>

      <div class="config-actions">
        <button @click="resetConfig" class="btn-secondary">Restaurar Padrões</button>
        <button @click="saveConfig" class="btn-primary">Salvar Configurações</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const CONFIG_STORAGE_KEY = 'bhumi-shop-config'

const defaultConfig = {
  storeName: 'BHUMI SHOP',
  storeDescription: 'Arte, Conhecimento e Criatividade',
  contactEmail: '',
  whatsapp: '',
  pixKey: '',
  paypalEmail: '',
  mercadopagoToken: '',
  freeShippingAbove: 199,
  defaultShipping: 15,
  productionDays: 5,
  returnPolicy: '',
  shippingInfo: ''
}

const config = ref({ ...defaultConfig })

onMounted(() => {
  loadConfig()
})

function loadConfig() {
  try {
    const saved = localStorage.getItem(CONFIG_STORAGE_KEY)
    if (saved) {
      config.value = { ...defaultConfig, ...JSON.parse(saved) }
    }
  } catch (e) {
    console.error('Erro ao carregar configurações:', e)
  }
}

function saveConfig() {
  try {
    localStorage.setItem(CONFIG_STORAGE_KEY, JSON.stringify(config.value))
    window.dispatchEvent(new Event('config-updated'))
    alert('Configurações salvas com sucesso!')
  } catch (e) {
    console.error('Erro ao salvar configurações:', e)
    alert('Erro ao salvar configurações')
  }
}

function resetConfig() {
  if (confirm('Tem certeza que deseja restaurar as configurações padrões?')) {
    config.value = { ...defaultConfig }
    localStorage.removeItem(CONFIG_STORAGE_KEY)
  }
}
</script>

<style scoped>
.config-page {
  padding: 3rem 1rem;
  max-width: 800px;
}

.page-title {
  font-size: 2.5rem;
  text-align: center;
  margin-bottom: 3rem;
}

.config-sections {
  display: flex;
  flex-direction: column;
  gap: 2rem;
}

.config-section {
  background: var(--bg-card);
  border: 1px solid var(--border-color);
  border-radius: 12px;
  padding: 2rem;
}

.config-section h2 {
  font-size: 1.25rem;
  margin-bottom: 1.5rem;
  color: var(--accent-purple-light);
}

.form-group {
  margin-bottom: 1.25rem;
}

.form-group:last-child {
  margin-bottom: 0;
}

.form-group label {
  display: block;
  margin-bottom: 0.5rem;
  font-weight: 600;
  color: var(--text-secondary);
}

.form-group input,
.form-group textarea {
  width: 100%;
  background: var(--bg-secondary);
  border: 1px solid var(--border-color);
  color: var(--text-primary);
  padding: 0.75rem;
  border-radius: 4px;
  font-size: 1rem;
}

.form-group input:focus,
.form-group textarea:focus {
  outline: none;
  border-color: var(--accent-purple);
}

.form-group small {
  display: block;
  margin-top: 0.25rem;
  color: var(--text-muted);
  font-size: 0.85rem;
}

.config-actions {
  display: flex;
  justify-content: space-between;
  gap: 1rem;
  margin-top: 1rem;
}

.btn-primary,
.btn-secondary {
  padding: 0.75rem 1.5rem;
  border-radius: 4px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
}

.btn-primary {
  background: var(--accent-purple);
  color: white;
  border: none;
}

.btn-primary:hover {
  background: var(--accent-purple-light);
}

.btn-secondary {
  background: transparent;
  color: var(--text-secondary);
  border: 1px solid var(--border-color);
}

.btn-secondary:hover {
  border-color: var(--accent-purple);
  color: var(--text-primary);
}

@media (max-width: 600px) {
  .config-actions {
    flex-direction: column;
  }
}
</style>
