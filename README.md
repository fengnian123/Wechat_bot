<h1 align='center'>Customer Service WeChat Bot with Agentic RAG Powered by Qwen or Mistral, using 🐫 CAMEL, Firecrawl</h1>

## ⚙️ Installation

System requirement: Supports Linux, MacOS, and Windows ; requires Python to be installed. It is recommended that the Python version range from 3.7.1 to 3.9.X, 3.8 is recommended, 3.10 and later versions are available on MacOS. 

Download the codes:

```bash
  git clone https://github.com/fengnian123/Wechat_bot.git
  cd Wechat_bot/
```

Install packages with `pip`:

```bash
  pip install -r requirements.txt
  pip install "camel-ai[all]==0.2.10"
```

### 📥 Completing configuration

First we need to change some environment variables to replace our own API. Open the `config.py` file in the root directory and fill in the following variables. That is:

- `os.environ["FIRECRAWL_API_KEY"] = "YOUR API"`: Replace `YOUR API` with the API_KEY of the firecrawl you got
- `knowledge = firecrawl.crawl(url="Knowledge base URL")`: Replace `Knowledge base URL` with the address of the crawled knowledge base
- `api_key="YOUR API"`: Replace `YOUR API` with the model API you got

```python
# config.py
os.environ["FIRECRAWL_API_KEY"] = "YOUR API"
os.makedirs('local_data', exist_ok=True)
firecrawl = Firecrawl()
knowledge = firecrawl.crawl(url="Knowledge base URL")
with open('local_data/qdrant_overview.md', 'w') as file:
    file.write(knowledge["data"][0]["markdown"])
qwen_model = ModelFactory.create(
            model_platform=ModelPlatformType.OPENAI_COMPATIBLE_MODEL,
            model_type="Qwen/Qwen2.5-32B-Instruct", 
            api_key="YOUR API",
            url="Model path",
            model_config_dict=QwenConfig(temperature=0.2).as_dict(),
        )
agent = ChatAgent(
            system_message="You're a helpful assistant",
            message_window_size=10,
            model=qwen_model
        )
```

### 🛠️ Prepare config information

A simple example is as follows. In a text exchange, we only need to pay attention to:

- `single_chat_prefix`: In a personal chat, the content starting with single_chat_prefix needs to trigger the bot; 
- `single_chat_reply_prefix`: The bot replies are prefixed with "[bot] "to distinguish real people. 
- `group_name_white_list`: In a group chat, the group name can be set to group_name_white_list. To apply to all group chats, enter "group_name_white_list": ["ALL_GROUP"]

Other configuration details, please see https://github.com/zhayujie/chatgpt-on-wechat?tab=readme-ov-file

```python
# config.json
{
  "model": "gpt-3.5-turbo",                                   
  "open_ai_api_key": "YOUR API KEY",                          
  "open_ai_api_base": "https://api.openai.com/v1",           
  "proxy": "",                                              
  "single_chat_prefix": ["bot", "@bot"],                
  "single_chat_reply_prefix": "[bot] ",                      
  "group_chat_prefix": ["@bot"],                              
  "group_name_white_list": [],
  "group_chat_in_one_session": [],              
  "image_create_prefix": [],                   
  "conversation_max_tokens": 1000,                         
  "speech_recognition": false,                                
  "group_speech_recognition": false,                          
  "voice_reply_voice": false,                                 
  "character_desc": "",  
  "subscribe_msg": "",
  "use_linkai": false,                                       
  "linkai_api_key": "",                                      
  "linkai_app_code": ""                                      
}
```



### 🎮 Start Running

Now we can run `app.py`

```bash
	python app.py
```

You'll see a QR code pop up. You can scan the code and confirm the login to wechat, and wechat will be treated as a wechat robot, and when we send a message to the wechat, we will automatically receive a reply.



### 🔧️ Notes

If you encounter other problems, please confirm the following：

1. Wechat robot accounts only support real-name authentication accounts. Or you will encounter errors about `wxsid`.
2. Using proxy networks or some public networks may experience `request` errors.
