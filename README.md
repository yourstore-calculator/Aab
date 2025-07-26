# Aab
import math

# =============================================
# Helper Functions - دوال مساعدة
# =============================================

def get_dimensions(is_per_meter=True):
    """
    Function to get width and height from the user and return area.
    دالة لأخذ العرض والطول من المستخدم وإرجاع المساحة
    """
    while True:
        try:
            if is_per_meter:
                print("\nالرجاء إدخال الأبعاد بالمتر:")
            width = float(input("ادخل العرض (متر): "))
            height = float(input("ادخل الطول/الارتفاع (متر): "))
            if width <= 0 or height <= 0:
                print("الأبعاد يجب أن تكون أرقاماً موجبة. الرجاء المحاولة مرة أخرى.")
                continue
            return width, height, (width * height)
        except ValueError:
            print("مدخل غير صحيح. الرجاء إدخال أرقام فقط.")

def calculate_door_extra_charge(area, standard_area=2.2):
    """
    Calculates the extra charge for doors larger than the standard size.
    (كل زيادة في المساحة 0.1 متر مربع = 2 ريال)
    """
    extra_charge = 0
    if area > standard_area:
        area_increase = area - standard_area
        # Calculate how many 0.1 increments are in the increase
        increments = math.ceil(area_increase / 0.1)
        extra_charge = increments * 2
        print(f"تمت إضافة مبلغ {extra_charge:.2f} ريال بسبب زيادة المساحة عن الحجم القياسي.")
    return extra_charge

# =============================================
# Main Calculation Functions - دوال الحساب الرئيسية
# =============================================

def calculate_windows():
    """Calculates the price for different types of windows."""
    print("""
    اختر نوع النافذة:
    1. دبل جلاس دبل فريم
    2. دبل جلاس سنجل فريم
    3. سنجل جلاس سنجل فريم (عادية)
    4. نوافذ السلايدنج (RMP)
    5. النوافذ الكهربائية
    """)
    choice = input("ادخل اختيارك (1-5): ")

    rate = 0
    cbm_factor = 0
    price = 0
    cbm = 0
    is_double_glass = False

    # --- Window Type Selection ---
    if choice == '1': # دبل جلاس دبل فريم
        is_double_glass = True
        cbm_factor = 0.13
        move_choice = input("اختر الحركة (1: ثابتة, 2: حركة, 3: حركتين): ")
        rates = {'1': 34, '2': 73, '3': 92}
        rate = rates.get(move_choice, 34)
    elif choice == '2': # دبل جلاس سنجل فريم
        is_double_glass = True
        cbm_factor = 0.07
        move_choice = input("اختر الحركة (1: ثابتة, 2: حركة, 3: حركتين): ")
        rates = {'1': 26, '2': 46, '3': 58}
        rate = rates.get(move_choice, 26)
    elif choice == '3': # سنجل جلاس سنجل فريم
        cbm_factor = 0.07
        move_choice = input("اختر الحركة (1: ثابتة, 2: حركة, 3: حركتين): ")
        rates = {'1': 20, '2': 43, '3': 47}
        rate = rates.get(move_choice, 20)
    elif choice == '4': # نوافذ السلايدنج RMP
        is_double_glass = True
        print("نوافذ السلايدنج RMP (دبل جلاس فقط)")
        _, _, area = get_dimensions()
        # النوع * المساحة + 10
        price = (58 * area) + 10 # Assuming '58' is the rate for double glass 2-movements
        cbm = 0.13 * area # Assuming same CBM as other double glass
        print_result(price, cbm)
        return
    elif choice == '5': # النوافذ الكهربائية
        is_double_glass = True # Assuming electric is double glass
        rate = 102
        cbm_factor = 0.13
    else:
        print("خيار غير صحيح.")
        return

    # --- Common Calculation for Meter-based Windows ---
    _, _, area = get_dimensions()
    price = rate * area
    cbm = cbm_factor * area

    # --- Add-ons ---
    # 1. الشبك
    add_mesh = input("\nهل تريد إضافة شبك؟ (نعم/لا): ").strip().lower()
    if add_mesh == 'نعم':
        print("اختر نوع الشبك (1: باب, 2: فولدنج, 3: سلايد):")
        mesh_choice = input("ادخل اختيارك: ")
        mesh_rates = {'1': 39, '2': 18, '3': 14}
        mesh_rate = mesh_rates.get(mesh_choice, 14)
        mesh_area = 0.5 * area
        mesh_cost = mesh_rate * mesh_area
        price += mesh_cost
        print(f"تمت إضافة تكلفة الشبك: {mesh_cost:.2f} ريال (لا يؤثر على الشحن)")

    # 2. ستارة داخلية
    if is_double_glass:
        add_curtain = input("هل تريد إضافة ستارة داخلية؟ (نعم/لا): ").strip().lower()
        if add_curtain == 'نعم':
            curtain_cost = 26 * area
            price += curtain_cost
            print(f"تمت إضافة تكلفة الستارة الداخلية: {curtain_cost:.2f} ريال")

    print_result(price, cbm)


def calculate_doors():
    """Calculates the price for different types of doors."""
    print("""
    اختر نوع الباب:
    1.  باب المدخل
    2.  أبواب WPC
    3.  أبواب الألمنيوم
    4.  باب دورات المياه
    5.  أبواب السحب
    6.  أبواب الفولدنج
    """)
    choice = input("ادخل اختيارك (1-6): ").strip()
    
    # Standard dimensions for unit-based doors
    standard_w, standard_h = 1.0, 2.2
    standard_area = standard_w * standard_h

    if choice == '1': # باب المدخل
        print("اختر مادة باب المدخل (1: زينك, 2: ستينلس ستيل, 3: كاست المنيوم):")
        mat_choice = input("ادخل اختيارك: ")
        rates = {'1': 66, '2': 120, '3': 168}
        rate = rates.get(mat_choice, 66)
        _, _, area = get_dimensions()
        price = (rate * area) + 10
        cbm = area * 0.2
        print_result(price, cbm)

    elif choice == '2': # أبواب WPC
        print("اختر نوع باب WPC:")
        print("1. فارغ (45 ريال)")
        print("2. مع خشب (50 ريال)")
        print("3. مع حشوة ضد الصوت (60 ريال)")
        print("4. مع فريم ألمينيوم (67 ريال)")
        print("5. سلايدنج WPC (65 ريال)")
        type_choice = input("ادخل اختيارك: ")
        prices = {'1': 45, '2': 50, '3': 60, '4': 67, '5': 65}
        base_price = prices.get(type_choice, 45)
        
        w, h, area = get_dimensions()
        
        # Calculate price based on size
        extra_charge = calculate_door_extra_charge(area, standard_area)
        price = base_price + extra_charge
        
        # Default CBM
        cbm = area * 0.11
        
        # تلبيسة للسقف
        add_cladding = input("هل تريد إضافة تلبيسة للسقف؟ (نعم/لا): ").strip().lower()
        if add_cladding == 'نعم':
            cladding_length = float(input("ادخل طول التلبيسة بالمتر: "))
            cladding_cost = math.ceil(cladding_length) * 10
            price += cladding_cost
            print(f"تمت إضافة تكلفة التلبيسة: {cladding_cost:.2f} ريال")
            # Adjust CBM
            cbm = (h + cladding_length) * w * 0.11
            print("تم تحديث حساب الشحن ليشمل التلبيسة.")

        print_result(price, cbm)

    elif choice == '3': # أبواب الألمنيوم
        print("اختر نوع باب الألمنيوم:")
        print("1. ألمنيوم فارغ (65 ريال)")
        print("2. ألمنيوم مع خشب (75 ريال)")
        print("3. فل ألمنيوم (85 ريال)")
        print("4. الباب المخفي (110 ريال)")
        print("5. باب ألمنيوم خارجي (61 ريال)")
        type_choice = input("ادخل اختيارك: ")
        prices = {'1': 65, '2': 75, '3': 85, '4': 110, '5': 61}
        base_price = prices.get(type_choice, 65)
        
        _, _, area = get_dimensions()
        extra_charge = calculate_door_extra_charge(area, standard_area)
        price = base_price + extra_charge
        cbm = area * 0.11
        print_result(price, cbm)

    elif choice == '4': # باب دورات المياه
        standard_area_wc = 0.1 * 2.2 # مقاس خاص
        print("اختر نوع باب دورة المياه:")
        print("1. النوع الجديد (55 ريال)")
        print("2. النوع الأقدم (45 ريال)")
        print("3. مخفي زجاجي (65 ريال)")
        type_choice = input("ادخل اختيارك: ")
        prices = {'1': 55, '2': 45, '3': 65}
        base_price = prices.get(type_choice, 55)

        _, _, area = get_dimensions()
        extra_charge = calculate_door_extra_charge(area, standard_area_wc)
        price = base_price + extra_charge
        cbm = area * 0.11
        print_result(price, cbm)
        
    elif choice == '5': # أبواب السحب
        print("اختر نوع باب السحب:")
        print("1. داخلي زجاج (38 ريال/متر)")
        print("2. داخلي متين (41 ريال/متر)")
        print("3. خارجي جزء مفتوح (55 ريال/متر)")
        print("4. خارجي جزئين مفتوحات (58 ريال/متر)")
        print("5. WPC سلايد (61 ريال/متر)")
        type_choice = input("ادخل اختيارك: ")
        rates = {'1': 38, '2': 41, '3': 55, '4': 58, '5': 61}
        rate = rates.get(type_choice, 38)

        _, _, area = get_dimensions()
        price = rate * area
        cbm = area * 0.15
        
        # Add-on check for curtains
        add_curtain = input("هل تريد إضافة ستارة داخلية؟ (نعم/لا): ").strip().lower()
        if add_curtain == 'نعم':
            curtain_cost = 26 * area
            price += curtain_cost
            print(f"تمت إضافة تكلفة الستارة الداخلية: {curtain_cost:.2f} ريال")

        print_result(price, cbm)
        
    elif choice == '6': # أبواب الفولدنج
        print("اختر نوع باب الفولدنج (1: داخلي, 2: خارجي):")
        type_choice = input("ادخل اختيارك: ")
        rates = {'1': 39, '2': 56}
        rate = rates.get(type_choice, 39)
        
        _, _, area = get_dimensions()
        price = rate * area
        cbm = area * 0.15

        # Add-on check for curtains
        add_curtain = input("هل تريد إضافة ستارة داخلية؟ (نعم/لا): ").strip().lower()
        if add_curtain == 'نعم':
            curtain_cost = 26 * area
            price += curtain_cost
            print(f"تمت إضافة تكلفة الستارة الداخلية: {curtain_cost:.2f} ريال")
            
        print_result(price, cbm)


def calculate_simple_meter_based(product_name, choices, cbm_factor):
    """A generic function for simple meter-based calculations."""
    print(f"\nاختر نوع {product_name}:")
    for key, (text, rate) in choices.items():
        print(f"{key}. {text} ({rate} ريال/متر)")
    
    choice = input("ادخل اختيارك: ")
    if choice not in choices:
        print("خيار غير صحيح.")
        return
        
    rate = choices[choice][1]
    _, _, area = get_dimensions()
    price = rate * area
    cbm = area * cbm_factor
    print_result(price, cbm)

def print_result(price, cbm):
    """Formats and prints the final result."""
    print("\n" + "="*20)
    print("      النتيجة النهائية")
    print("="*20)
    print(f"💰 السعر الإجمالي: {price:.2f} ريال عماني")
    print(f"📦 حجم الشحن: {cbm:.3f} CBM")
    print("="*20 + "\n")


# =============================================
# Main Program Loop - البرنامج الرئيسي
# =============================================

def main():
    """Main function to run the calculator."""
    while True:
        print("""
    ======================================
    ======= حاسبة الأسعار والشحن ========
    ======================================
    الرجاء اختيار فئة المنتج:
    
    1.  النوافذ
    2.  الأبواب
    3.  سكاي لايت (Skylight)
    4.  كارتن وول (Curtain Wall)
    5.  شتر دور الخارجي (Shutter Door)
    6.  أبواب الحدائق الخارجية (كاست المنيوم)
    7.  حواجز
    
    0.  للخروج من البرنامج
    """)
        main_choice = input("ادخل رقم الفئة: ").strip()

        if main_choice == '1':
            calculate_windows()
        elif main_choice == '2':
            calculate_doors()
        elif main_choice == '3':
            calculate_simple_meter_based(
                "السكاي لايت",
                {'1': ("بدون المكينة", 56), '2': ("مع المكينة", 145)},
                0.13
            )
        elif main_choice == '4':
            calculate_simple_meter_based(
                "الكارتن وول",
                {'1': ("الثقيل", 56), '2': ("الأخف", 45)},
                0.15
            )
        elif main_choice == '5':
            print("\n--- حساب الشتر دور الخارجي ---")
            rate = 28
            _, _, area = get_dimensions()
            price = rate * area
            cbm = area * 0.2
            print_result(price, cbm)
        elif main_choice == '6':
            print("\n--- حساب أبواب الحدائق الخارجية (كاست المنيوم) ---")
            rate = 91
            _, _, area = get_dimensions()
            price = rate * area
            cbm = area * 0.2
            print_result(price, cbm)
        elif main_choice == '7':
             calculate_simple_meter_based(
                "الحواجز",
                {'1': ("حواجز الدرج", 43), '2': ("حواجز الحمام", 32)},
                0.05
            )
        elif main_choice == '0':
            print("\nشكراً لاستخدامك الحاسبة. إلى اللقاء!")
            break
        else:
            print("\nخيار غير صحيح. الرجاء إدخال رقم من القائمة.")
        
        input("اضغط Enter للمتابعة إلى عملية حسابية أخرى...")


if __name__ == "__main__":
    main()

