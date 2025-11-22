# -*- coding: utf-8 -*-
import requests, os, time, json
from colorama import init, Fore, Style

init(autoreset=True)

def banner():
    os.system('clear' if os.name == 'posix' else 'cls')
    print(Fore.CYAN + Style.BRIGHT + """
    ╔══════════════════════════════════╗
    ║            INSTA TOOL            ║
    ║      Instagram Profile Checker   ║
    ╚══════════════════════════════════╝
    """ + Style.RESET_ALL)
    print(Fore.MAGENTA + "         INSTA PROFİLE TOOL\n" + Style.RESET_ALL)

def get_cookies_and_headers():
    # Otomatik çalışan güncel header (2025 çalışıyor)
    return {
        'User-Agent': 'Mozilla/5.0 (Linux; Android 12; SM-G998B) AppleWebKit/537.36 Chrome/129.0.6668.100 Mobile Safari/537.36',
        'X-IG-App-ID': '936619743392459',
        'X-CSRFToken': 'missing',  # Gerekirse manuel ekleyebilirsin ama çoğu zaman çalışıyor
        'Cookie': 'sessionid=; csrftoken=;'  # İstersen session ekle, çoğu zaman gerekmiyor
    }

def insta_sorgu(username):
    url = f"https://www.instagram.com/api/v1/users/web_profile_info/?username={username}"
    headers = get_cookies_and_headers()
    
    try:
        r = requests.get(url, headers=headers, timeout=15)
        if r.status_code != 200:
            return None
        data = r.json()
        user = data['data']['user']
        
        # Profil bilgilerini al
        profile = {
            'username': user['username'],
            'full_name': user['full_name'],
            'id': user['id'],
            'is_private': 'Yes' if user['is_private'] else 'No',
            'is_verified': 'Yes' if user['is_verified'] else 'No',
            'followers': user['edge_followed_by']['count'],
            'following': user['edge_follow']['count'],
            'posts': user['edge_owner_to_timeline_media']['count'],
            'bio': user.get('biography_with_entities', {}).get('raw_text', 'No bio'),
            'external_url': user.get('external_url', 'No link'),
        }
        
        # Profil fotoğrafı base64
        pic_url = user['profile_pic_url_hd']
        pic = requests.get(pic_url).content
        profile['photo_base64'] = pic
        
        return profile
    except:
        return None

def send_telegram(token, chat_id, profile):
    url = f"https://api.telegram.org/bot{token}/sendPhoto"
    
    text = f"""
*INSTAGRAM PROFILE INFO*

*Username:* @{profile['username']}
*Full Name:* {profile['full_name']}
*User ID:* `{profile['id']}`
*Private:* {profile['is_private']}
*Verified:* {profile['is_verified']}
*Followers:* `{profile['followers']}`
*Following:* `{profile['following']}`
*Posts:* `{profile['posts']}`
*Bio:* {profile['bio']}
*Link:* {profile['external_url']}

    """.strip()
    
    files = {'photo': ('profile.jpg', profile['photo_base64'])}
    data = {'chat_id': chat_id, 'caption': text, 'parse_mode': 'Markdown'}
    
    try:
        requests.post(url, data=data, files=files)
        print(Fore.GREEN + "[+] Profile sent to Telegram!" + Style.RESET_ALL)
    except:
        print(Fore.RED + "[-] Telegram send failed. Check Token/ID!" + Style.RESET_ALL)

def main():
    banner()
    print(Fore.YELLOW + "Instagram Profile Checker \n" + Style.RESET_ALL)
    
    token = "8184022892:AAGhzk1EkLiMnJyAoX_q4mXfXr_SNQcW4MU"
    chat_id = 6410106464
    
    while True:
        username = input(Fore.CYAN + "\n[?] Instagram username (exit to quit): " + Style.RESET_ALL).strip()
        if username.lower() in ['exit', 'quit', 'q']:
            print(Fore.MAGENTA + "See you king! 👑" + Style.RESET_ALL)
            break
            
        print(Fore.BLUE + f"Checking @{username}..." + Style.RESET_ALL)
        profile = insta_sorgu(username)
        
        if not profile:
            print(Fore.RED + "[-] Profile not found or private/blocked!" + Style.RESET_ALL)
            continue
            
        print(Fore.GREEN + f"[+] @{username} found! Sending..." + Style.RESET_ALL)
        send_telegram(token, chat_id, profile)
        time.sleep(1)

if __name__ == "__main__":
    try:
        main()
    except KeyboardInterrupt:
        print(Fore.RED + "\n\nExited." + Style.RESET_ALL)
