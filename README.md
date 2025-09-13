import React, { useState } from "react";

const SCRIPT_URL = "[https://script.google.com/macros/s/AKfycbw1nw8rQtVaPpzEkMIAqVPgdtJQCI2WFY074e-mR44sKXditM5zpSKYHnKtLuvuqBs1/exec](https://script.googleusercontent.com/macros/echo?user_content_key=AehSKLgn4NOPZU3yxbT2cTBQF8hu48iXYp96ohGEfZzv0KujZUAeWyDsRRAV2M5spn-NNOUNLtcZVcJiHO4mgXSljK8LZIVLvkL9F93SWZcw-fQI610jJ_kTJaQczfpcLkrnafUjIWyeoVnOYaiaYRMVCxaJYnKKVQGKKiIMW4Cosfr2xhbhjQgwYKAqEx92dq4jt2IvFYrn5nLfU6BD34-GK-tl1kNlb6YsiuhjEQOvGFTucouyLYtdNxW6TARqz-3y7IwpD024KkjxrPXdknryKh3p6Xw5FQ&lib=MGthlI05eQ21eaX7oSGWM8ufF-xda-4F0)";

const durianTypes = [
  "หมอนทอง",
  "ก้านยาว",
  "ชะนี",
  "หลงลับแล",
  "หลินลับแล",
  "กระดุม",
  "พวงมณี",
  "นกหยิบ",
  "กบชายน้ำ"
];

const price = 500;

function App() {
  const [customerName, setCustomerName] = useState("");
  const [phone, setPhone] = useState("");
  const [order, setOrder] = useState(
    durianTypes.reduce((acc, t) => ({ ...acc, [t]: 0 }), {})
  );

  const handleSubmit = async (e) => {
    e.preventDefault();

    const selected = Object.entries(order)
      .filter(([_, qty]) => qty > 0)
      .map(([type, qty]) => ({ type, qty, total: qty * price }));

    if (selected.length === 0) {
      alert("กรุณาเลือกทุเรียนอย่างน้อย 1 ลูก");
      return;
    }
    if (!customerName || !phone) {
      alert("กรุณากรอกชื่อและเบอร์โทร");
      return;
    }

    const payload = {
      customerName,
      phone,
      items: selected,
      grandTotal: selected.reduce((s, it) => s + it.total, 0),
      status: "รอชำระ"
    };

    try {
      const res = await fetch(SCRIPT_URL, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(payload)
      });

      const json = await res.json();
      if (json.status === "success") {
        alert("บันทึกการพรีออเดอร์เรียบร้อยแล้ว!");
        setCustomerName("");
        setPhone("");
        setOrder(durianTypes.reduce((acc, t) => ({ ...acc, [t]: 0 }), {}));
      } else {
        alert("เกิดข้อผิดพลาด: " + (json.message || "ไม่ทราบสาเหตุ"));
      }
    } catch (err) {
      console.error(err);
      alert("ส่งข้อมูลไม่สำเร็จ");
    }
  };

  return (
    <div style={{ padding: "20px" }}>
      <h1>ระบบพรีออเดอร์ทุเรียน</h1>
      <form onSubmit={handleSubmit}>
        <div>
          <label>ชื่อ-นามสกุล: </label>
          <input
            type="text"
            value={customerName}
            onChange={(e) => setCustomerName(e.target.value)}
          />
        </div>
        <div>
          <label>เบอร์โทร: </label>
          <input
            type="text"
            value={phone}
            onChange={(e) => setPhone(e.target.value)}
          />
        </div>

        <h3>เลือกสายพันธุ์ทุเรียน (ลูกละ 500)</h3>
        {durianTypes.map((type) => (
          <div key={type}>
            <label>{type}: </label>
            <input
              type="number"
              min="0"
              value={order[type]}
              onChange={(e) =>
                setOrder({ ...order, [type]: Number(e.target.value) })
              }
            />
          </div>
        ))}

        <button type="submit" style={{ marginTop: "20px" }}>
          สั่งจอง
        </button>
      </form>
    </div>
  );
}

export default App;
