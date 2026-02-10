#!/usr/bin/env python3
"""
گزارش‌زن خودکار برای سروش‌پلاس
نسخه: 1.0
مخصوص ترمکس
"""

import os
import sys
import time
import json
import random
import requests
import threading
from datetime import datetime
from colorama import Fore, Style, init
from concurrent.futures import ThreadPoolExecutor, as_completed

# فعال کردن رنگ‌ها در ترمکس
init(autoreset=True)

class SoroushPlusReporter:
    def __init__(self):
        self.session = requests.Session()
        self.report_count = 0
        self.success_count = 0
        self.failed_count = 0
        self.running = False
        self.config_file = "soroush_config.json"
        self.proxies_file = "proxies.txt"
        self.accounts_file = "accounts.txt"
        
        # تنظیمات پیش‌فرض
        self.default_config = {
            "max_reports_per_account": 10,
            "delay_between_reports": 5,
            "use_proxy": False,
            "random_delay": True,
            "save_logs": True,
            "report_reasons": [
                "ارسال محتوای نامناسب",
                "کلاهبرداری",
                "اسپم",
                "محتواي خشونت آميز",
                "آزار و اذيت",
                "نقض قوانين",
                "حساب جعلي",
                "محتوای غیراخلاقی",
                "تبلیغات غیرمجاز"
            ]
        }
        
        self.load_config()
        self.setup_headers()
    
    def setup_headers(self):
        """تنظیم هدرهای HTTP"""
        self.headers = {
            'User-Agent': 'Mozilla/5.0 (Linux; Android 10; SM-G975F) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.120 Mobile Safari/537.36',
            'Accept': 'application/json, text/plain, */*',
            'Accept-Language': 'fa-IR,fa;q=0.9,en-US;q=0.8,en;q=0.7',
            'Accept-Encoding': 'gzip, deflate, br',
            'Connection': 'keep-alive',
            'Content-Type': 'application/json',
            'Origin': 'https://web.splus.ir',
            'Referer': 'https://web.splus.ir/',
            'Sec-Fetch-Dest': 'empty',
            'Sec-Fetch-Mode': 'cors',
            'Sec-Fetch-Site': 'same-site'
        }
    
    def load_config(self):
        """بارگذاری تنظیمات"""
        if os.path.exists(self.config_file):
            try:
                with open(self.config_file, 'r', encoding='utf-8') as f:
                    self.config = json.load(f)
                print(Fore.GREEN + "[+] تنظیمات بارگذاری شد")
            except:
                self.config = self.default_config
                self.save_config()
        else:
            self.config = self.default_config
            self.save_config()
    
    def save_config(self):
        """ذخیره تنظیمات"""
        try:
            with open(self.config_file, 'w', encoding='utf-8') as f:
                json.dump(self.config, f, ensure_ascii=False, indent=4)
            print(Fore.GREEN + "[+] تنظیمات ذخیره شد")
        except Exception as e:
            print(Fore.RED + f"[-] خطا در ذخیره تنظیمات: {e}")
    
    def print_banner(self):
        """نمایش بنر برنامه"""
        banner = f"""
{Fore.CYAN}╔══════════════════════════════════════════╗
║        {Fore.YELLOW}گزارش‌زن سروش‌پلاس{Fore.CYAN}              ║
║     {Fore.WHITE}مخصوص ترمکس - نسخه 1.0{Fore.CYAN}           ║
║                                            ║
║ {Fore.GREEN}✔ گزارش خودکار حساب‌های کاربری{Fore.CYAN}           ║
║ {Fore.GREEN}✔ پشتیبانی از چندین حساب{Fore.CYAN}                 ║
║ {Fore.GREEN}✔ تأخیر تصادفی برای جلوگیری از شناسایی{Fore.CYAN}  ║
║ {Fore.GREEN}✔ سیستم پروکسی{Fore.CYAN}                           ║
╚══════════════════════════════════════════╝
{Style.RESET_ALL}
        """
        print(banner)
    
    def clear_screen(self):
        """پاک کردن صفحه"""
        os.system('clear' if os.name != 'nt' else 'cls')
    
    def create_accounts_file(self):
        """ایجاد فایل حساب‌ها"""
        sample_accounts = [
            "توکن_حساب_اول",
            "توکن_حساب_دوم",
            "توکن_حساب_سوم"
        ]
        
        with open(self.accounts_file, 'w', encoding='utf-8') as f:
            f.write("# قرار دهید tokens حساب‌های خود را در این فایل\n")
            f.write("# هر توکن در یک خط جداگانه\n")
            f.write("# توکن‌ها را از برنامه سروش‌پلاس دریافت کنید\n\n")
            for account in sample_accounts:
                f.write(f"{account}\n")
        
        print(Fore.YELLOW + f"[!] فایل {self.accounts_file} ایجاد شد")
        print(Fore.CYAN + "[*] لطفاً توکن‌های حساب‌های خود را در این فایل قرار دهید")
    
    def create_proxies_file(self):
        """ایجاد فایل پروکسی"""
        sample_proxies = [
            "http://user:pass@ip:port",
            "socks5://user:pass@ip:port",
            "http://ip:port"
        ]
        
        with open(self.proxies_file, 'w', encoding='utf-8') as f:
            f.write("# آدرس پروکسی‌ها را در این فایل قرار دهید\n")
            f.write("# فرمت: پروتکل://کاربر:رمز@آی‌پی:پورت\n")
            f.write("# یا: پروتکل://آی‌پی:پورت\n\n")
            for proxy in sample_proxies:
                f.write(f"{proxy}\n")
        
        print(Fore.YELLOW + f"[!] فایل {self.proxies_file} ایجاد شد")
    
    def load_accounts(self):
        """بارگذاری حساب‌ها از فایل"""
        accounts = []
        
        if not os.path.exists(self.accounts_file):
            print(Fore.RED + "[-] فایل حساب‌ها یافت نشد")
            self.create_accounts_file()
            return accounts
        
        try:
            with open(self.accounts_file, 'r', encoding='utf-8') as f:
                for line in f:
                    line = line.strip()
                    if line and not line.startswith('#'):
                        accounts.append(line)
            
            if accounts:
                print(Fore.GREEN + f"[+] {len(accounts)} حساب بارگذاری شد")
            else:
                print(Fore.YELLOW + "[!] هیچ حسابی در فایل یافت نشد")
                
        except Exception as e:
            print(Fore.RED + f"[-] خطا در بارگذاری حساب‌ها: {e}")
        
        return accounts
    
    def load_proxies(self):
        """بارگذاری پروکسی‌ها"""
        proxies = []
        
        if not os.path.exists(self.proxies_file):
            print(Fore.YELLOW + "[!] فایل پروکسی یافت نشد")
            self.create_proxies_file()
            return proxies
        
        try:
            with open(self.proxies_file, 'r', encoding='utf-8') as f:
                for line in f:
                    line = line.strip()
                    if line and not line.startswith('#'):
                        proxies.append(line)
            
            if proxies:
                print(Fore.GREEN + f"[+] {len(proxies)} پروکسی بارگذاری شد")
            else:
                print(Fore.YELLOW + "[!] هیچ پروکسی در فایل یافت نشد")
                
        except Exception as e:
            print(Fore.RED + f"[-] خطا در بارگذاری پروکسی‌ها: {e}")
        
        return proxies
    
    def get_random_proxy(self, proxies_list):
        """انتخاب تصادفی پروکسی"""
        if not proxies_list:
            return None
        
        proxy = random.choice(proxies_list)
        proxy_dict = {}
        
        if proxy.startswith('http'):
            proxy_dict['http'] = proxy
            proxy_dict['https'] = proxy.replace('http://', 'https://') if 'http://' in proxy else proxy
        elif proxy.startswith('socks'):
            proxy_dict['http'] = proxy
            proxy_dict['https'] = proxy
        
        return proxy_dict
    
    def get_account_info(self, token):
        """دریافت اطلاعات حساب"""
        url = "https://api.splus.ir/api/v1/auth/me"
        
        headers = self.headers.copy()
        headers['Authorization'] = f'Bearer {token}'
        
        try:
            response = self.session.get(url, headers=headers, timeout=10)
            
            if response.status_code == 200:
                data = response.json()
                username = data.get('data', {}).get('username', 'نامشخص')
                phone = data.get('data', {}).get('phoneNumber', 'نامشخص')
                return username, phone
            else:
                return None, None
                
        except Exception as e:
            print(Fore.RED + f"[-] خطا در دریافت اطلاعات حساب: {e}")
            return None, None
    
    def send_report(self, token, target_username, report_reason, proxy=None):
        """ارسال گزارش"""
        url = "https://api.splus.ir/api/v2/report"
        
        headers = self.headers.copy()
        headers['Authorization'] = f'Bearer {token}'
        
        payload = {
            "reported": target_username,
            "reason": report_reason,
            "type": "user"
        }
        
        try:
            if proxy and self.config['use_proxy']:
                response = self.session.post(
                    url, 
                    headers=headers, 
                    json=payload, 
                    timeout=15,
                    proxies=proxy
                )
            else:
                response = self.session.post(
                    url, 
                    headers=headers, 
                    json=payload, 
                    timeout=15
                )
            
            self.report_count += 1
            
            if response.status_code == 200:
                self.success_count += 1
                return True, response.status_code
            else:
                self.failed_count += 1
                return False, response.status_code
                
        except requests.exceptions.RequestException as e:
            self.failed_count += 1
            return False, str(e)
        except Exception as e:
            self.failed_count += 1
            return False, str(e)
    
    def save_log(self, log_data):
        """ذخیره لاگ"""
        if not self.config['save_logs']:
            return
        
        log_file = f"reports_log_{datetime.now().strftime('%Y%m%d')}.txt"
        
        try:
            with open(log_file, 'a', encoding='utf-8') as f:
                timestamp = datetime.now().strftime('%Y-%m-%d %H:%M:%S')
                f.write(f"[{timestamp}] {log_data}\n")
        except:
            pass
    
    def show_settings_menu(self):
        """منوی تنظیمات"""
        while True:
            self.clear_screen()
            self.print_banner()
            
            print(Fore.YELLOW + "\n⚙️ تنظیمات فعلی:\n")
            
            for key, value in self.config.items():
                if isinstance(value, list):
                    value_str = ', '.join(value[:3]) + ('...' if len(value) > 3 else '')
                else:
                    value_str = str(value)
                
                print(f"  {Fore.CYAN}{key}: {Fore.WHITE}{value_str}")
            
            print(Fore.YELLOW + "\n📝 ویرایش تنظیمات:\n")
            print("1. تغییر حداکثر گزارش‌های هر حساب")
            print("2. تغییر تأخیر بین گزارش‌ها")
            print("3. فعال/غیرفعال کردن پروکسی")
            print("4. فعال/غیرفعال کردن تأخیر تصادفی")
            print("5. فعال/غیرفعال کردن ذخیره لاگ")
            print("6. ویرایش دلایل گزارش")
            print("7. مشاهده فایل حساب‌ها")
            print("8. مشاهده فایل پروکسی‌ها")
            print("9. بازگشت")
            
            choice = input(Fore.GREEN + "\n❓ انتخاب کنید: " + Style.RESET_ALL).strip()
            
            if choice == '1':
                try:
                    new_value = int(input("حداکثر گزارش‌های هر حساب: "))
                    self.config['max_reports_per_account'] = max(1, new_value)
                    self.save_config()
                except:
                    print(Fore.RED + "مقدار نامعتبر!")
                    time.sleep(2)
            
            elif choice == '2':
                try:
                    new_value = int(input("تأخیر بین گزارش‌ها (ثانیه): "))
                    self.config['delay_between_reports'] = max(1, new_value)
                    self.save_config()
                except:
                    print(Fore.RED + "مقدار نامعتبر!")
                    time.sleep(2)
            
            elif choice == '3':
                self.config['use_proxy'] = not self.config['use_proxy']
                status = "فعال" if self.config['use_proxy'] else "غیرفعال"
                print(Fore.GREEN + f"پروکسی {status} شد")
                self.save_config()
                time.sleep(1)
            
            elif choice == '4':
                self.config['random_delay'] = not self.config['random_delay']
                status = "فعال" if self.config['random_delay'] else "غیرفعال"
                print(Fore.GREEN + f"تأخیر تصادفی {status} شد")
                self.save_config()
                time.sleep(1)
            
            elif choice == '5':
                self.config['save_logs'] = not self.config['save_logs']
                status = "فعال" if self.config['save_logs'] else "غیرفعال"
                print(Fore.GREEN + f"ذخیره لاگ {status} شد")
                self.save_config()
                time.sleep(1)
            
            elif choice == '6':
                self.edit_report_reasons()
            
            elif choice == '7':
                self.view_accounts_file()
            
            elif choice == '8':
                self.view_proxies_file()
            
            elif choice == '9':
                break
    
    def edit_report_reasons(self):
        """ویرایش دلایل گزارش"""
        print(Fore.YELLOW + "\n📝 دلایل فعلی گزارش:\n")
        for i, reason in enumerate(self.config['report_reasons'], 1):
            print(f"{i}. {reason}")
        
        print(Fore.YELLOW + "\n1. افزودن دلیل جدید")
        print("2. حذف دلیل")
        print("3. بازگشت")
        
        choice = input(Fore.GREEN + "\n❓ انتخاب کنید: " + Style.RESET_ALL).strip()
        
        if choice == '1':
            new_reason = input("دلیل جدید: ").strip()
            if new_reason:
                self.config['report_reasons'].append(new_reason)
                self.save_config()
                print(Fore.GREEN + "دلیل جدید اضافه شد")
        
        elif choice == '2':
            try:
                index = int(input("شماره دلیل برای حذف: ")) - 1
                if 0 <= index < len(self.config['report_reasons']):
                    removed = self.config['report_reasons'].pop(index)
                    self.save_config()
                    print(Fore.GREEN + f"دلیل '{removed}' حذف شد")
                else:
                    print(Fore.RED + "شماره نامعتبر!")
            except:
                print(Fore.RED + "ورودی نامعتبر!")
        
        time.sleep(1)
    
    def view_accounts_file(self):
        """مشاهده فایل حساب‌ها"""
        self.clear_screen()
        print(Fore.YELLOW + "📋 محتوای فایل حساب‌ها:\n")
        
        if os.path.exists(self.accounts_file):
            try:
                with open(self.accounts_file, 'r', encoding='utf-8') as f:
                    content = f.read()
                    print(content)
            except:
                print(Fore.RED + "خطا در خواندن فایل")
        else:
            print(Fore.RED + "فایل وجود ندارد")
        
        input(Fore.CYAN + "\nبرای ادامه Enter بزنید...")
    
    def view_proxies_file(self):
        """مشاهده فایل پروکسی‌ها"""
        self.clear_screen()
        print(Fore.YELLOW + "📋 محتوای فایل پروکسی‌ها:\n")
        
        if os.path.exists(self.proxies_file):
            try:
                with open(self.proxies_file, 'r', encoding='utf-8') as f:
                    content = f.read()
                    print(content)
            except:
                print(Fore.RED + "خطا در خواندن فایل")
        else:
            print(Fore.RED + "فایل وجود ندارد")
        
        input(Fore.CYAN + "\nبرای ادامه Enter بزنید...")
    
    def start_reporting(self):
        """شروع فرآیند گزارش‌دهی"""
        self.clear_screen()
        self.print_banner()
        
        print(Fore.YELLOW + "\n🎯 شروع گزارش‌دهی\n")
        
        # دریافت اطلاعات هدف
        target_username = input("نام کاربری هدف: ").strip()
        if not target_username:
            print(Fore.RED + "نام کاربری نمی‌تواند خالی باشد!")
            time.sleep(2)
            return
        
        try:
            total_reports = int(input("تعداد کل گزارش‌ها: "))
            total_reports = max(1, total_reports)
        except:
            print(Fore.RED + "تعداد نامعتبر!")
            time.sleep(2)
            return
        
        # بارگذاری حساب‌ها و پروکسی‌ها
        accounts = self.load_accounts()
        if not accounts:
            print(Fore.RED + "هیچ حسابی برای گزارش‌دهی یافت نشد!")
            time.sleep(2)
            return
        
        proxies = []
        if self.config['use_proxy']:
            proxies = self.load_proxies()
        
        # نمایش خلاصه
        print(Fore.GREEN + f"\n📊 خلاصه عملیات:")
        print(f"  هدف: {target_username}")
        print(f"  تعداد کل گزارش‌ها: {total_reports}")
        print(f"  تعداد حساب‌ها: {len(accounts)}")
        print(f"  حداکثر گزارش هر حساب: {self.config['max_reports_per_account']}")
        print(f"  پروکسی: {'فعال' if self.config['use_proxy'] and proxies else 'غیرفعال'}")
        
        confirm = input(Fore.RED + "\n⚠️ آیا مطمئن هستید؟ (y/n): " + Style.RESET_ALL).lower()
        if confirm != 'y':
            print(Fore.YELLOW + "عملیات لغو شد")
            time.sleep(1)
            return
        
        # شروع گزارش‌دهی
        self.running = True
        self.report_count = 0
        self.success_count = 0
        self.failed_count = 0
        
        print(Fore.GREEN + "\n🚀 شروع گزارش‌دهی...\n")
        
        try:
            with ThreadPoolExecutor(max_workers=min(3, len(accounts))) as executor:
                futures = []
                
                # توزیع گزارش‌ها بین حساب‌ها
                reports_per_account = min(self.config['max_reports_per_account'], 
                                        total_reports // max(1, len(accounts)))
                
                if reports_per_account < 1:
                    reports_per_account = 1
                
                for i, token in enumerate(accounts):
                    if not self.running:
                        break
                    
                    # دریافت اطلاعات حساب
                    username, phone = self.get_account_info(token)
                    account_info = f"حساب {i+1}"
                    if username:
                        account_info += f" ({username})"
                    
                    print(Fore.CYAN + f"[*] شروع گزارش‌دهی با {account_info}")
                    
                    # ارسال گزارش‌ها از این حساب
                    for j in range(reports_per_account):
                        if not self.running or self.success_count >= total_reports:
                            break
                        
                        # انتخاب تصادفی دلیل
                        reason = random.choice(self.config['report_reasons'])
                        
                        # انتخاب پروکسی
                        proxy = self.get_random_proxy(proxies) if proxies else None
                        
                        # ایجاد تأخیر
                        if self.config['random_delay']:
                            delay = random.uniform(
                                self.config['delay_between_reports'] * 0.5,
                                self.config['delay_between_reports'] * 1.5
                            )
                        else:
                            delay = self.config['delay_between_reports']
                        
                        time.sleep(delay)
                        
                        # ارسال گزارش
                        future = executor.submit(
                            self.send_report, 
                            token, 
                            target_username, 
                            reason, 
                            proxy
                        )
                        futures.append((future, account_info, reason))
                        
                        # نمایش وضعیت
                        status_line = f"[{self.report_count}/{total_reports}] {account_info}: {reason}"
                        print(Fore.YELLOW + status_line)
            
            # بررسی نتایج
            print(Fore.GREEN + "\n✅ گزارش‌دهی تکمیل شد!\n")
            
        except KeyboardInterrupt:
            print(Fore.YELLOW + "\n\n⏹️ عملیات توسط کاربر متوقف شد")
            self.running = False
        
        # نمایش آمار نهایی
        self.show_statistics()
        
        input(Fore.CYAN + "\nبرای ادامه Enter بزنید...")
    
    def show_statistics(self):
        """نمایش آمار"""
        print(Fore.CYAN + "📈 آمار نهایی:")
        print(f"  کل گزارش‌های ارسال شده: {self.report_count}")
        print(f"  گزارش‌های موفق: {self.success_count}")
        print(f"  گزارش‌های ناموفق: {self.failed_count}")
        
        if self.report_count > 0:
            success_rate = (self.success_count / self.report_count) * 100
            print(f"  نرخ موفقیت: {success_rate:.1f}%")
    
    def test_account(self):
        """تست اتصال حساب"""
        self.clear_screen()
        print(Fore.YELLOW + "\n🔧 تست اتصال حساب\n")
        
        token = input("توکن حساب: ").strip()
        if not token:
            print(Fore.RED + "توکن نمی‌تواند خالی باشد!")
            time.sleep(2)
            return
        
        print(Fore.CYAN + "\n[*] در حال تست اتصال...")
        
        username, phone = self.get_account_info(token)
        
        if username:
            print(Fore.GREEN + f"✅ اتصال موفق!")
            print(f"  نام کاربری: {username}")
            if phone:
                print(f"  شماره تلفن: {phone}")
        else:
            print(Fore.RED + "❌ اتصال ناموفق!")
            print(Fore.YELLOW + "   لطفاً توکن را بررسی کنید")
        
        input(Fore.CYAN + "\nبرای ادامه Enter بزنید...")
    
    def show_help(self):
        """نمایش راهنما"""
        self.clear_screen()
        print(Fore.YELLOW + """
📖 راهنمای استفاده:

1. نحوه دریافت توکن سروش‌پلاس:
   - وارد وبسایت سروش‌پلاس شوید (web.splus.ir)
   - لاگین کنید
   - در مرورگر، کلید F12 را بزنید
   - به تب Network بروید
   - یک عمل انجام دهید (مثلاً پیامی بفرستید)
   - در لیست درخواست‌ها، درخواستی با auth را پیدا کنید
   - در Headers آن، Authorization را پیدا کنید
   - توکن را کپی کنید (بعد از Bearer)

2. آماده‌سازی:
   - ابتدا حساب‌های خود را در فایل accounts.txt قرار دهید
   - اگر نیاز به پروکسی دارید، در فایل proxies.txt قرار دهید
   - تنظیمات را مطابق نیاز خود تغییر دهید

3. نکات مهم:
   ⚠️  از این ابزار فقط برای اهداف قانونی استفاده کنید
   ⚠️  گزارش‌های نادرست ممکن است باعث مسدودی حساب شما شود
   ⚠️  از تأخیرهای مناسب استفاده کنید تا شناسایی نشوید
   ⚠️  تعداد گزارش‌های زیاد از یک حساب ممکن است مشکوک باشد

4. توصیه‌ها:
   - از چندین حساب با آی‌پی‌های مختلف استفاده کنید
   - بین گزارش‌ها تأخیر تصادفی قرار دهید
   - دلایل گزارش را متنوع انتخاب کنید
   - تعداد گزارش‌های هر حساب را محدود کنید
        """)
        
        input(Fore.CYAN + "\nبرای ادامه Enter بزنید...")
    
    def main_menu(self):
        """منوی اصلی"""
        while True:
            self.clear_screen()
            self.print_banner()
            
            # نمایش وضعیت
            print(Fore.CYAN + f"\n📊 وضعیت: {len(self.load_accounts())} حساب | {len(self.load_proxies())} پروکسی")
            
            # منوی اصلی
            print(Fore.YELLOW + "\n📱 منوی اصلی:\n")
            print("1. 🚀 شروع گزارش‌دهی خودکار")
            print("2. ⚙️  تنظیمات")
            print("3. 🔧 تست اتصال حساب")
            print("4. 📋 مشاهده حساب‌ها")
            print("5. 🔗 مشاهده پروکسی‌ها")
            print("6. 📖 راهنما")
            print("7. 💾 ذخیره تنظیمات")
            print("8. 🗑️  پاک کردن داده‌ها")
            print("0. ❌ خروج")
            
            choice = input(Fore.GREEN + "\n❓ انتخاب کنید: " + Style.RESET_ALL).strip()
            
            if choice == '1':
                self.start_reporting()
            elif choice == '2':
                self.show_settings_menu()
            elif choice == '3':
                self.test_account()
            elif choice == '4':
                self.view_accounts_file()
            elif choice == '5':
                self.view_proxies_file()
            elif choice == '6':
                self.show_help()
            elif choice == '7':
                self.save_config()
                print(Fore.GREEN + "✅ تنظیمات ذخیره شد")
                time.sleep(1)
            elif choice == '8':
                self.clear_data()
            elif choice == '0':
                print(Fore.YELLOW + "\n👋 خروج از برنامه...")
                break
            else:
                print(Fore.RED + "گزینه نامعتبر!")
                time.sleep(1)
    
    def clear_data(self):
        """پاک کردن داده‌ها"""
        print(Fore.RED + "\n⚠️  آیا مطمئن هستید که می‌خواهید همه داده‌ها را پاک کنید؟")
        confirm = input("همه فایل‌های ذخیره شده پاک خواهند شد (y/n): ").lower()
        
        if confirm == 'y':
            files_to_remove = [
                self.config_file,
                self.accounts_file,
                self.proxies_file
            ]
            
            # پاک کردن لاگ‌های قدیمی
            for file in os.listdir('.'):
                if file.startswith('reports_log_') and file.endswith('.txt'):
                    files_to_remove.append(file)
            
            removed_count = 0
            for file in files_to_remove:
                if os.path.exists(file):
                    try:
                        os.remove(file)
                        print(Fore.YELLOW + f"[-] {file} پاک شد")
                        removed_count += 1
                    except:
                        print(Fore.RED + f"خطا در پاک کردن {file}")
            
            print(Fore.GREEN + f"\n✅ {removed_count} فایل پاک شد")
            time.sleep(2)
            
            # بازسازی فایل‌های پیش‌فرض
            self.config = self.default_config
            self.save_config()

def main():
    """تابع اصلی"""
    try:
        reporter = SoroushPlusReporter()
        reporter.main_menu()
    except KeyboardInterrupt:
        print(Fore.YELLOW + "\n\n👋 خروج از برنامه...")
        sys.exit(0)
    except Exception as e:
        print(Fore.RED + f"\n❌ خطای غیرمنتظره: {e}")
        input("برای خروج Enter بزنید...")

if __name__ == "__main__":
    # بررسی وابستگی‌ها
    try:
        import requests
        import colorama
    except ImportError:
        print("نصب وابستگی‌ها...")
        os.system("pip install requests colorama")
        print("وابستگی‌ها نصب شدند. برنامه را مجدداً اجرا کنید.")
        sys.exit(1)
    
    main()