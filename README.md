const { Markup, Telegraf } = require("telegraf");
const { Pagination } = require("telegraf-pagination");

const TOKEN = "YOUR_BOT_TOKEN";
const bot = new Telegraf(TOKEN);

const fakeData = Array.from({ length: 10 }, (_, i) => ({
  id: i,
  title: `Item ${i + 1}`,
}));

bot.command("pagination", async (ctx) => {
  try {
    const pagination = new Pagination({
      data: fakeData,
      header: (currentPage, pageSize, total) =>
        `${currentPage}-page of total ${total}`,
      format: (item, index) => `${index + 1}. ${item.title}`,
      pageSize: 8,
      rowSize: 4,
      isButtonsMode: false,
      buttonModeOptions: {
        isSimpleArray: true,
        titleKey: '',
      },
      isEnabledDeleteButton: true,
      onSelect: (item, index) => {
        ctx.reply(item.title);
      },
      messages: {
        firstPage: "First page",
        lastPage: "Last page",
        prev: "◀️",
        next: "▶️",
        delete: "🗑",
      },
      inlineCustomButtons: [
        Markup.button.callback('Title custom button', 'your_callback_name')
      ]
    });

    pagination.handleActions(bot);

    const text = await pagination.text();
    const keyboard = await pagination.keyboard();
    await ctx.replyWithHTML(text, keyboard);
  } catch (error) {
    console.error("Error handling pagination command:", error);
    ctx.reply("An error occurred while processing your request.");
  }
});

bot.launch().then(() => {
  console.log("Bot is running");
}).catch((error) => {
  console.error("Error launching bot:", error);
});
