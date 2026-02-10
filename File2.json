const express = require('express');
const axios = require('axios');
const cors = require('cors');
const app = express();

app.use(cors()); // GitHub Pages'dan so'rov yuborishga ruxsat beradi
app.use(express.json());

// BU YERGA IIKO DAN OLGAN API LOGIN KODINGIZNI YOZING
const IIKO_API_LOGIN = "SIZNING_IIKO_API_LOGININGIZ"; 

// 1. iiko dan Token olish funksiyasi
async function getIikoToken() {
    try {
        const response = await axios.post('https://api-ru.iiko.services/api/1/access_token', {
            apiLogin: IIKO_API_LOGIN
        });
        return response.data.token;
    } catch (error) {
        console.error("Token olishda xato:", error.message);
        return null;
    }
}

// 2. Stol raqami bo'yicha hisobni olish
app.get('/get-bill/:tableId', async (req, res) => {
    const tableId = req.params.tableId;
    const token = await getIikoToken();

    if (!token) return res.status(500).json({ error: "iiko token olinmadi" });

    try {
        // Avval restoraningiz (organization) ID-sini olish
        const orgs = await axios.get('https://api-ru.iiko.services/api/1/organizations', {
            headers: { 'Authorization': `Bearer ${token}` }
        });
        const orgId = orgs.data.organizations[0].id;

        // iiko dan stol ma'lumotlarini so'rash
        const bill = await axios.post('https://api-ru.iiko.services/api/1/order/by_table', {
            organizationIds: [orgId],
            tableIds: [tableId]
        }, {
            headers: { 'Authorization': `Bearer ${token}` }
        });

        res.json(bill.data);
    } catch (error) {
        res.status(500).json({ error: "iiko ma'lumotlarni berishda xato qildi" });
    }
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server ${PORT}-portda yondi`));
