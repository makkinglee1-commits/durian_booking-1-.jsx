import { useState } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";

const durianTypes = [
  "ก้านยาว",
  "หมอนทอง",
  "ชะนี",
  "กบชายน้ำ",
  "กบแม่เฒ่า",
  "กระดุม",
  "พวงมณี",
  "หลงลับแล",
  "หลินลับแล",
];

export default function DurianBooking() {
  const [order, setOrder] = useState(
    durianTypes.reduce((acc, type) => ({ ...acc, [type]: 0 }), {})
  );
  const [customerName, setCustomerName] = useState("");
  const [phone, setPhone] = useState("");
  const price = 500;
  const total = Object.values(order).reduce(
    (sum, qty) => sum + qty * price,
    0
  );

  const handleChange = (type, value) => {
    setOrder((prev) => ({ ...prev, [type]: Math.max(0, Number(value)) }));
  };

  const handleSubmit = () => {
    const selected = Object.entries(order).filter(([_, qty]) => qty > 0);
    if (selected.length === 0) {
      alert("กรุณาเลือกทุเรียนอย่างน้อย 1 ลูก");
      return;
    }
    if (!customerName || !phone) {
      alert("กรุณากรอกชื่อและเบอร์โทร");
      return;
    }

    const data = {
      customerName,
      phone,
      items: selected.map(([type, qty]) => ({ type, qty, total: qty * price })),
      grandTotal: total,
    };

    console.log("ส่งข้อมูลไป Google Sheets:", data);
    alert("บันทึกคำสั่งซื้อเรียบร้อย!");

    // reset form
    setCustomerName("");
    setPhone("");
    setOrder(durianTypes.reduce((acc, type) => ({ ...acc, [type]: 0 }), {}));
  };

  return (
    <div className="min-h-screen flex items-center justify-center bg-green-50 p-6">
      <Card className="w-full max-w-2xl shadow-xl rounded-2xl">
        <CardContent className="p-6 space-y-4">
          <h1 className="text-2xl font-bold text-center">จองทุเรียนออนไลน์</h1>
          <p className="text-center text-gray-600">ลูกละ 500 บาท</p>

          {/* ข้อมูลลูกค้า */}
          <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
            <div>
              <label className="block mb-2 font-semibold">ชื่อผู้จอง</label>
              <input
                type="text"
                className="w-full border rounded-lg p-2"
                value={customerName}
                onChange={(e) => setCustomerName(e.target.value)}
              />
            </div>
            <div>
              <label className="block mb-2 font-semibold">เบอร์โทร</label>
              <input
                type="tel"
                className="w-full border rounded-lg p-2"
                value={phone}
                onChange={(e) => setPhone(e.target.value)}
              />
            </div>
          </div>

          {/* เลือกสายพันธุ์และจำนวน */}
          <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
            {durianTypes.map((type, i) => (
              <div key={i} className="border rounded-lg p-3">
                <label className="block font-semibold mb-1">{type}</label>
                <input
                  type="number"
                  min="0"
                  className="w-full border rounded-lg p-2"
                  value={order[type]}
                  onChange={(e) => handleChange(type, e.target.value)}
                />
              </div>
            ))}
          </div>

          {/* แสดงราคา */}
          <div className="text-lg font-bold text-center">
            ราคารวมทั้งหมด: {total.toLocaleString()} บาท
          </div>

          {/* ปุ่มชำระเงิน */}
          <div className="flex flex-col items-center space-y-3">
            <img
              src="/qrcode-promptpay.png"
              alt="PromptPay QR"
              className="w-40 h-40 border"
            />
            <Button className="w-full" onClick={handleSubmit}>
              ยืนยันการจองและชำระเงิน
            </Button>
          </div>
        </CardContent>
      </Card>
    </div>
  );
}
