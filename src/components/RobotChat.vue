<template>
    <div class="robot-chat" @click="toggleModal" @mouseenter="showTooltip" @mouseleave="hideTooltip">
        <img src="@/assets/robot.gif" alt="Robot" class="robot">
        <span v-if="showTooltipText" class="tooltip">Precisa de ajuda?</span>
        <div v-if="isModalOpen" class="modal-overlay" @click="closeModal">
            <div class="modal-content" @click.stop>
                <h2>Seja bem-vindo ao Copybase BI</h2>
                <h4>Instruções para uso:</h4>
                <p>1 - Clique em <strong>"Carregue sua planilha"</strong> para gerar os gráficos</p>
                <p>2 - Para visualizar qualquer um dos gráficos menores, clique nele</p>
                <p>3 - Para fechar o popup do gráfico, basta clicar fora</p>
                <p>4 - Para mais dúvidas, acesse o link no footer e fale comigo</p>
                <p>5 - Atualmente, o código deste site é dedicado a carregar dados desta planilha:</p>
                <p><a href="https://docs.google.com/spreadsheets/d/1Nc1dPGsyJGFPIBf2VIknC_zZWZFy5oqke2X5jXdpUBI/edit#gid=440460926" target="_blank">Veja a planilha e baixe!</a></p>
                <button @click="closeModal" class="close-btn">Fechar</button>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'RobotChat',
    data() {
        return {
            isModalOpen: false,
            showTooltipText: false,
        };
    },
    methods: {
        toggleModal() {
            this.isModalOpen = true;
        },
        closeModal() {
            this.isModalOpen = false;
        },
        showTooltip() {
            this.showTooltipText = true;
        },
        hideTooltip() {
            this.showTooltipText = false;
        },
    },
};
</script>

<style>
@import "../styles/RobotChat";
</style>