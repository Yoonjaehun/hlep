const fs = require("fs");
const txt = fs.readFileSync("test.n", "utf8");
//코드 분리
const codeArr = txt.split("\r\n");
const validData = [];
codeArr.forEach((code) => {
  if (code.charAt(0) === "(" && code.charAt(code.length - 1) === ")") {
    validData.push(code);
  }
});

const cleanData = validData.map((validData) => validData.substring(1, validData.length - 1));

const c_codeArr = [];

cleanData.forEach((data) => {
  const type = data.substring(0, 4);
  if (type === "echo") {
    c_codeArr.push("printf(" + data.substring(5, data.length - 1) + "\\n”);");
  } else if (type === "cat ") {
    const catCode = data.substring(4, data.length);
    if (catCode.includes(" > ")) {
      const fileNames = catCode.split(" > ");
      c_codeArr.push("f1 = fopen(“" + fileNames[0] + "”, “r”);");
      c_codeArr.push("f2 = fopen(“" + fileNames[1] + "”, “w”);");
      c_codeArr.push("while((c = getc(f1)) != EOF)");
      c_codeArr.push("fputc((int)c,f2);");
      c_codeArr.push("fclose(f2);");
      c_codeArr.push("fclose(f1);");
    } else {
      c_codeArr.push("f1 = fopen(“" + catCode + "”, “r”);");
      c_codeArr.push("while((c = getc(f1)) != EOF)");
      c_codeArr.push('printf("%c", c);');
      c_codeArr.push("fclose(f1);");
    }
  }
});

const c_code = "#include <stdio.h>\nint main(){\n" + c_codeArr.join("\n") + "\nreturn 0;\n}";
fs.writeFileSync("test.c", "\ufeff" + c_code, { encoding: "utf8" });

