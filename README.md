await fetch(`https://api.jsonbin.io/v3/b/${BIN_ID}`, {
    method: "PUT",
    headers: {
        "Content-Type": "application/json",
        "X-Master-Key": JSONBIN_KEY
    },
    body: JSON.stringify({ record: lista }) // Garanta que `record` é um array
});
