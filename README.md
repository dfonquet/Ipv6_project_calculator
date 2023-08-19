
# 🌐 IPv6 Subnetting Calculator with Python

## Project Overview

In this project, I've developed an IPv6 subnetting calculator that caters to the needs of a network engineer, like myself,
who is also striving for IPv6 certification. Crafting tools tailored to the exigencies of our daily operations has become imperative. 
Gratitude is owed to dynamic programming languages like Python, which empower us to materialize these solutions.

Beyond its technical prowess, this program boasts versatility. It's deployable on your personal computing device, 
leveraging the Tkinter library to forge a visually engaging interface complete with input fields catering to IPv6 network specifics,
the prefix, and the quantum of desired subnets.

## Program Features

Execution is a breeze. Merely replicate the provided code within a Python (.py) file and initiate the runtime. A prerequisite is the presence of Tkinter within your Python environment.





<details>
<summary>Click to view code snippet</summary>

```python
# -*- coding: utf-8 -*-
"""
Created on Sat Aug  19 21:05:06 2023

@author: Daftiel
"""


import ipaddress
import tkinter as tk
from tkinter import ttk
from tkinter import messagebox
import webbrowser

class IPv6SubnetCalculatorApp:
    def __init__(self, window):
        self.window = window
        self.window.title("Calculadora IPv6 y Subneteo")
        
        self.create_widgets()
        
    def create_widgets(self):
        style = ttk.Style()
        style.configure("TButton", padding=10, font=("Helvetica", 12))
        
        # Etiquetas y campos de entrada
        ttk.Label(self.window, text="Dirección de Red IPv6:").pack(pady=(20, 5))
        self.red_entry = ttk.Entry(self.window)
        self.red_entry.pack(pady=(0, 10))
        
        ttk.Label(self.window, text="Prefijo de la Red:").pack()
        self.prefijo_entry = ttk.Entry(self.window)
        self.prefijo_entry.pack(pady=(0, 10))
        
        ttk.Label(self.window, text="Número de Subredes:").pack()
        self.subredes_entry = ttk.Entry(self.window)
        self.subredes_entry.pack(pady=(0, 20))
        
        # Botones
        self.calcular_button = ttk.Button(self.window, text="Calcular Subredes", command=self.calcular_click)
        self.calcular_button.pack(side=tk.LEFT, padx=(0, 10))
        
        self.info_button = ttk.Button(self.window, text="Obtener Información", command=self.info_click)
        self.info_button.pack(side=tk.LEFT, padx=(0, 10))
        
        self.subneteo_button = ttk.Button(self.window, text="Subneteo Paso a Paso", command=self.subneteo_click)
        self.subneteo_button.pack(side=tk.LEFT, padx=(0, 10))
        
        self.best_practices_frame = ttk.Frame(self.window)
        self.best_practices_frame.pack(pady=(10, 0))
        
        self.best_practices_label = ttk.Label(self.best_practices_frame, text="Mejores Prácticas IPv6 LACNIC:")
        self.best_practices_label.pack(side=tk.LEFT)
        
        self.best_practices_button1 = ttk.Button(self.best_practices_frame, text="Arquitectura IPv6 y Subnetting", command=self.open_url1)
        self.best_practices_button1.pack(side=tk.LEFT, padx=(10, 5))
        
        self.best_practices_button2 = ttk.Button(self.best_practices_frame, text="Despliegue IPv6", command=self.open_url2)
        self.best_practices_button2.pack(side=tk.LEFT, padx=(5, 10))
        
        # Resultados
        self.result_label = ttk.Label(self.window, text="Resultados:")
        self.result_label.pack(pady=(20, 5))
        
        self.result_text = tk.Text(self.window, height=10, width=50)
        self.result_text.pack(fill=tk.BOTH, expand=True)
        
        self.result_scrollbar = ttk.Scrollbar(self.window, command=self.result_text.yview)
        self.result_scrollbar.pack(fill=tk.Y, side=tk.RIGHT)
        self.result_text.config(yscrollcommand=self.result_scrollbar.set)
        
        self.detail_label = ttk.Label(self.window, text="Detalle del Subneteo:")
        self.detail_label.pack(pady=(10, 5))
        
        self.detail_text = tk.Text(self.window, height=10, width=50, state=tk.DISABLED)
        self.detail_text.pack(fill=tk.BOTH, expand=True)
        
        self.credit_label = ttk.Label(self.window, text="Creado por D@ftiel 2023")
        self.credit_label.pack(pady=(10, 20))

    def calcular_subredes_ipv6(self, red, prefijo, num_subredes):
        network = ipaddress.IPv6Network(f'{red}/{prefijo}', strict=False)
        subredes = list(network.subnets(new_prefix=prefijo + num_subredes))
        return subredes
    
    def obtener_info_red(self, red):
        network = ipaddress.IPv6Network(red, strict=False)
        info = (
            f"Dirección de Red: {network.network_address}\n"
            f"Prefijo: {network.prefixlen}\n"
            f"Máscara de Subred: {network.netmask}\n"
            f"Dirección de Broadcast: {network.broadcast_address}"
        )
        return info
    
    def calcular_click(self):
        red_ipv6 = self.red_entry.get()
        prefijo = int(self.prefijo_entry.get())
        num_subredes = int(self.subredes_entry.get())

        subredes = self.calcular_subredes_ipv6(red_ipv6, prefijo, num_subredes)
        
        self.result_text.config(state=tk.NORMAL)
        self.result_text.delete(1.0, tk.END)
        self.result_text.insert(tk.END, f"Subredes resultantes de {red_ipv6}/{prefijo}:\n\n")
        self.result_text.insert(tk.END, f"Se han calculado {len(subredes)} subredes:\n")
        self.result_text.insert(tk.END, f"- Dirección de Red: {red_ipv6}/{prefijo}\n")
        self.result_text.insert(tk.END, f"- Número de Subredes: {num_subredes}\n\n")
        self.result_text.insert(tk.END, "Subredes resultantes:\n")
        for i, subred in enumerate(subredes, start=1):
            self.result_text.insert(tk.END, f"Subred {i}:\n")
            self.result_text.insert(tk.END, f"- Dirección de Red: {subred.network_address}/{subred.prefixlen}\n")
            self.result_text.insert(tk.END, f"- Rango de Direcciones: {subred.network_address + 1} - {subred.broadcast_address - 1}\n\n")
        self.result_text.config(state=tk.DISABLED)
        
        self.update_detail_text(red_ipv6, prefijo, num_subredes, subredes)
    
    def info_click(self):
        red_ipv6 = self.red_entry.get()
        info = self.obtener_info_red(red_ipv6)
        self.show_info_popup("Información de la Red IPv6", info)
    
    def subneteo_click(self):
        red_ipv6 = self.red_entry.get()
        prefijo = int(self.prefijo_entry.get())
        num_subredes = int(self.subredes_entry.get())
        
        self.result_text.config(state=tk.NORMAL)
        self.result_text.delete(1.0, tk.END)
        self.result_text.insert(tk.END, f"Subneteo Paso a Paso de {red_ipv6}/{prefijo}:\n\n")
        
        network = ipaddress.IPv6Network(f'{red_ipv6}/{prefijo}', strict=False)
        subnet_iterator = network.subnets(new_prefix=prefijo + num_subredes)
        for subnet in subnet_iterator:
            self.result_text.insert(tk.END, f"Subred: {subnet.network_address}/{subnet.prefixlen}\n")
            self.result_text.insert(tk.END, f"Rango de Direcciones: {subnet.network_address + 1} - {subnet.broadcast_address - 1}\n\n")
        
        self.result_text.config(state=tk.DISABLED)
    
    def update_detail_text(self, red_ipv6, prefijo, num_subredes, subredes):
        self.detail_text.config(state=tk.NORMAL)
        self.detail_text.delete(1.0, tk.END)
        self.detail_text.insert(tk.END, f"Detalle del Subneteo de {red_ipv6}/{prefijo}:\n\n")
        
        network = ipaddress.IPv6Network(f'{red_ipv6}/{prefijo}', strict=False)
        self.detail_text.insert(tk.END, f"Dirección de Red Original: {network.network_address}/{network.prefixlen}\n\n")
        
        for i, subnet in enumerate(subredes, start=1):
            self.detail_text.insert(tk.END, f"Subred {i}:\n")
            self.detail_text.insert(tk.END, f"- Dirección de Red: {subnet.network_address}/{subnet.prefixlen}\n")
            self.detail_text.insert(tk.END, f"- Rango de Direcciones: {subnet.network_address + 1} - {subnet.broadcast_address - 1}\n\n")
        
        self.detail_text.config(state=tk.DISABLED)
    
    def show_info_popup(self, title, info):
        messagebox.showinfo(title, info)
    
    def open_url1(self):
        url = "https://blog.lacnic.net/ipv6/arquitectura-ipv6-y-subnetting-una-guia-para-ingenieros-y-operadores-de-redes"
        webbrowser.open(url)
    
    def open_url2(self):
        url = "https://www.lacnic.net/despliegaIPv6"
        webbrowser.open(url)

def main():
    window = tk.Tk()
    app = IPv6SubnetCalculatorApp(window)
    window.mainloop()

if __name__ == "__main__":
    main()
