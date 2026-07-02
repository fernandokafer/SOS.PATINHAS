// server.js
const WebSocket = require('ws');

const PORT = 8080;
const wss = new WebSocket.Server({ port: PORT });

console.log("=========================================");
console.log("   SISTEMA DE RASTREAMENTO DE PETS v1.0   ");
console.log(`      Servidor rodando na porta ${PORT}      `);
console.log("=========================================\n");

// Coordenadas iniciais simuladas (Região Central)
let lat = -23.550520;
let lng = -46.633308;

wss.on('connection', (ws) => {
    console.log("[STATUS] Cliente conectado ao painel.");

    // Simula o envio de dados de localização do pet a cada 3 segundos
    const interval = setInterval(() => {
        if (ws.readyState === WebSocket.OPEN) {
            // Altera levemente a latitude e longitude para simular movimento real
            lat += (Math.random() - 0.5) * 0.0009;
            lng += (Math.random() - 0.5) * 0.0009;

            const dadosPet = {
                nome: "Apollo",
                status: "Em Movimento",
                bateria: Math.floor(Math.random() * (95 - 88 + 1)) + 88,
                lat: lat.toFixed(6),
                lng: lng.toFixed(6),
                ultimaAtualizacao: new Date().toLocaleTimeString()
            };

            console.log(`[GPS] ${dadosPet.nome} -> Lat: ${dadosPet.lat} | Lng: ${dadosPet.lng} | Bateria: ${dadosPet.bateria}%`);
            ws.send(JSON.stringify(dadosPet));
        }
    }, 3000);

    ws.on('error', (error) => {
        console.error("[ERRO] Erro na conexão com o cliente:", error.message);
    });

    ws.on('close', () => {
        console.log("[STATUS] Cliente desconectado.");
        clearInterval(interval);
    });
});
