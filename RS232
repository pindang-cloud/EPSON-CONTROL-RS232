import tkinter as tk
from tkinter import ttk, messagebox
import serial
import serial.tools.list_ports
import time

class ProjectorControlGUI:
    def __init__(self, root):
        self.root = root
        self.root.title("Epson Projector Control")
        self.root.geometry("400x300")
        self.projector = None
        
        # Frame utama
        main_frame = ttk.Frame(root, padding="10")
        main_frame.grid(row=0, column=0, sticky=(tk.W, tk.E, tk.N, tk.S))
        
        # Port Selection
        ttk.Label(main_frame, text="Pilih Port:").grid(row=0, column=0, padx=5, pady=5)
        self.port_combobox = ttk.Combobox(main_frame, width=20)
        self.port_combobox.grid(row=0, column=1, padx=5, pady=5)
        
        # Tombol refresh port
        ttk.Button(main_frame, text="Refresh Ports", command=self.refresh_ports).grid(row=0, column=2, padx=5, pady=5)
        
        # Status Frame
        status_frame = ttk.LabelFrame(main_frame, text="Status", padding="10")
        status_frame.grid(row=1, column=0, columnspan=3, padx=5, pady=10, sticky=(tk.W, tk.E))
        
        self.status_label = ttk.Label(status_frame, text="Tidak Terhubung")
        self.status_label.grid(row=0, column=0, padx=5)
        
        # Control Frame
        control_frame = ttk.LabelFrame(main_frame, text="Kontrol", padding="10")
        control_frame.grid(row=2, column=0, columnspan=3, padx=5, pady=10, sticky=(tk.W, tk.E))
        
        # Tombol kontrol
        ttk.Button(control_frame, text="Hubungkan", command=self.connect).grid(row=0, column=0, padx=5, pady=5)
        ttk.Button(control_frame, text="Putuskan", command=self.disconnect).grid(row=0, column=1, padx=5, pady=5)
        ttk.Button(control_frame, text="Power ON", command=self.power_on).grid(row=1, column=0, padx=5, pady=5)
        ttk.Button(control_frame, text="Power OFF", command=self.power_off).grid(row=1, column=1, padx=5, pady=5)
        ttk.Button(control_frame, text="Cek Status", command=self.check_status).grid(row=1, column=2, padx=5, pady=5)
        
        # Inisialisasi list port
        self.refresh_ports()
    
    def refresh_ports(self):
        """Memperbarui daftar port yang tersedia"""
        ports = [port.device for port in serial.tools.list_ports.comports()]
        self.port_combobox['values'] = ports
        if ports:
            self.port_combobox.set(ports[0])  # Pilih port pertama jika ada
    
    def connect(self):
        """Menghubungkan ke proyektor"""
        try:
            selected_port = self.port_combobox.get()
            if not selected_port:
                messagebox.showerror("Error", "Pilih port terlebih dahulu!")
                return
                
            # Cek apakah port sudah terbuka
            if self.projector and self.projector.is_open:
                messagebox.showinfo("Info", "Port sudah terhubung!")
                return
            
            self.projector = serial.Serial(
                port=selected_port,
                baudrate=9600,
                bytesize=serial.EIGHTBITS,
                parity=serial.PARITY_NONE,
                stopbits=serial.STOPBITS_ONE,
                timeout=1
            )
            self.status_label.config(text=f"Terhubung ke {selected_port}")
            messagebox.showinfo("Sukses", f"Berhasil terhubung ke port {selected_port}")
        except Exception as e:
            messagebox.showerror("Error", f"Gagal terhubung: {str(e)}")

    def disconnect(self):
        """Memutuskan koneksi dari proyektor"""
        if self.projector and self.projector.is_open:
            self.projector.close()
            self.status_label.config(text="Tidak Terhubung")
            messagebox.showinfo("Info", "Koneksi diputus")
        else:
            messagebox.showwarning("Peringatan", "Tidak ada koneksi yang aktif")

    def send_command(self, command):
        """Mengirim perintah ke proyektor"""
        if not self.projector or not self.projector.is_open:
            messagebox.showerror("Error", "Proyektor tidak terhubung!")
            return None
            
        try:
            self.projector.write(command.encode())
            time.sleep(0.1)
            response = self.projector.readline()
            return response.decode().strip()
        except Exception as e:
            messagebox.showerror("Error", f"Gagal mengirim perintah: {str(e)}")
            return None
    
    def power_on(self):
        """Menyalakan proyektor"""
        response = self.send_command("PWR ON\r")
        if response:
            messagebox.showinfo("Info", "Perintah Power ON dikirim")
            self.check_status()
    
    def power_off(self):
        """Mematikan proyektor"""
        response = self.send_command("PWR OFF\r")
        if response:
            messagebox.showinfo("Info", "Perintah Power OFF dikirim")
            self.check_status()
    
    def check_status(self):
        """Mengecek status proyektor"""
        response = self.send_command("\r*pow=?#\r")
        if response:
            status = "Menyala" if "ON" in response else "Mati"
            self.status_label.config(text=f"Status: {status}")
            messagebox.showinfo("Status Proyektor", f"Proyektor sedang {status}")

def main():
    root = tk.Tk()
    app = ProjectorControlGUI(root)
    root.mainloop()

if __name__ == "__main__":
    main()
