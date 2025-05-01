# -*- coding: utf-8 -*-

import websocket
import json
import threading
import time
import random
import sqlite3
import ast
from colorama import Fore, init
import os

init()

def load_tokens():
    tokens = []
    try:
        with open('tokens.txt', 'r', encoding='utf-8') as f:
            tokens = [line.strip() for line in f if line.strip()]
        print(f"{Fore.GREEN}[+] 성공적으로 {len(tokens)} 개의 토큰을 불러왔습니다.{Fore.RESET}")
    except FileNotFoundError:
        print(f"{Fore.RED}[!] tokens.txt를 찾을수 없습니다{Fore.RESET}")
    except Exception as e:
        print(f"{Fore.RED}[!] 토큰을 로딩하는데 문제가 생겼습니다: {e}{Fore.RESET}")
    return tokens

def get_random_properties():
    return {
        "os": random.choice(["Windows", "MacOS", "Linux"]),
        "browser": random.choice(["Chrome", "Firefox", "Safari"]),
        "device": "",
        "system_locale": "ko-KR",
        "browser_user_agent": "",
        "browser_version": "",
        "os_version": "",
        "referrer": "",
        "referring_domain": "",
        "referrer_current": "",
        "referring_domain_current": "",
        "release_channel": "stable",
        "client_build_number": random.randint(180000, 190000),
        "client_event_source": None
    }

class DiscordSocket(websocket.WebSocketApp):
    def __init__(self, token):
        self.token = token
        self.socket_headers = {
            "Accept-Encoding": "gzip, deflate, br",
            "Accept-Language": "ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7",
            "Cache-Control": "no-cache",
            "Pragma": "no-cache",
            "Sec-WebSocket-Extensions": "permessage-deflate; client_max_window_bits",
            "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36"
        }
        super().__init__(
            "wss://gateway.discord.gg/?encoding=json&v=9",
            header=self.socket_headers,
            on_open=self.on_open,
            on_message=self.on_message,
            on_error=self.on_error,
            on_close=self.on_close
        )
        
        status = random.choice(["online", "idle", "dnd"])
        self.auth = {
            "op": 2,
            "d": {
                "token": self.token,
                "properties": get_random_properties(),
                "presence": {
                    "status": status,
                    "since": 0,
                    "activities": [{
                        "name": "상태 메시지",
                        "type": 4,
                        "state": "디스코드 토큰 온라인",
                        "emoji": {
                            "name": "⚪" if status == "online" else "🌙" if status == "idle" else "🔴"  # 상태에 따른 이모지
                        }
                    }],
                    "afk": False
                },
                "compress": False,
                "client_state": {
                    "guild_versions": {},
                    "highest_last_message_id": "0",
                    "read_state_version": 0,
                    "user_guild_settings_version": -1,
                    "user_settings_version": -1,
                    "private_channels_version": "0"
                }
            }
        }

    def on_open(self, ws):
        print(f"{Fore.GREEN}[+] 웹소켓에 접속했습니다{Fore.RESET}")
        self.send(json.dumps(self.auth))
        
    def on_message(self, ws, message):
        decoded = json.loads(message)
        if decoded["op"] == 10:
            threading.Thread(target=self.heartbeat, args=(decoded["d"]["heartbeat_interval"] / 1000,), daemon=True).start()
            
    def on_error(self, ws, error):
        print(f"{Fore.RED}[!] 에러: {error}{Fore.RESET}")
        
    def on_close(self, ws, close_status_code, close_msg):
        print(f"{Fore.YELLOW}[-] 연결 종료{Fore.RESET}")
        
    def heartbeat(self, interval):
        while self.sock and self.sock.connected:
            self.send(json.dumps({"op": 1, "d": None}))
            time.sleep(interval)

def start_token(token):
    ws = DiscordSocket(token)
    ws.run_forever()
    return ws

def main():
    os.system('cls' if os.name == 'nt' else 'clear')
    print(f"{Fore.CYAN}Discord Token Onliner{Fore.RESET}")
    print(f"{Fore.YELLOW}토큰 로딩중...{Fore.RESET}")
    
    tokens = load_tokens()
    print(f"{Fore.GREEN}로드 완료 {len(tokens)} 토큰{Fore.RESET}")
    
    threads = []
    for token in tokens:
        thread = threading.Thread(target=start_token, args=(token,), daemon=True)
        threads.append(thread)
        thread.start()
        time.sleep(0.1)
        
    print(f"{Fore.GREEN}모든 토큰이 온라인이 되었습니다!{Fore.RESET}")
    
    try:
        while True:
            time.sleep(1)
    except KeyboardInterrupt:
        print(f"{Fore.YELLOW}Shuting Down...{Fore.RESET}")
        
if __name__ == "__main__":
    main()
