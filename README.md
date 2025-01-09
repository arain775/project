.data
    prompt_space_full: .asciiz "Parking is full. Exiting...\n"
    prompt_vehicle_num: .asciiz "Enter Vehicle Number: "
    prompt_vehicle_type: .asciiz "Enter Vehicle Type (1 for car, 2 for bike): "
    prompt_parking_fee_car: .asciiz "Parking fee for car: $10\n"
    prompt_parking_fee_bike: .asciiz "Parking fee for bike: $5\n"
    prompt_continue: .asciiz "Do you want to park another vehicle? (yes=1, no=0): "
    newline: .asciiz "\n"
    max_spaces: .word 10   # Maximum parking spaces
    current_count: .word 0  # Current number of parked vehicles

.text
    .globl main

main:
    # Load current count and max spaces
    la $t0, current_count
    lw $t1, 0($t0)        # Load current count into $t1
    la $t2, max_spaces
    lw $t3, 0($t2)        # Load max spaces into $t3

check_space:
    # Check if parking is full
    bge $t1, $t3, space_full_message

parking_loop:
    # Prompt for vehicle number
    li $v0, 4
    la $a0, prompt_vehicle_num
    syscall
    li $v0, 5
    syscall
    move $t4, $v0        # Store vehicle number in $t4

    # Prompt for vehicle type
    li $v0, 4
    la $a0, prompt_vehicle_type
    syscall
    li $v0, 5
    syscall
    move $t5, $v0        # Store vehicle type in $t5

    # Determine and print parking fee based on vehicle type
    li $t6, 1            # Vehicle type for car
    beq $t5, $t6, print_car_fee
    li $t6, 2            # Vehicle type for bike
    beq $t5, $t6, print_bike_fee

    # If vehicle type is invalid, loop back
    j parking_loop

print_car_fee:
    li $v0, 4
    la $a0, prompt_parking_fee_car
    syscall
    j ask_continue

print_bike_fee:
    li $v0, 4
    la $a0, prompt_parking_fee_bike
    syscall

ask_continue:
    # Prompt to continue or exit
    li $v0, 4
    la $a0, prompt_continue
    syscall
    li $v0, 5
    beqz $v0, exit        # If user inputs 0, exit

    # Increment current count
    addi $t1, $t1, 1
    sw $t1, 0($t0)

    # Check if parking is full after increment
    bge $t1, $t3, space_full_message

    # Loop back for next vehicle
    j parking_loop

exit:
    # Exit program
    li $v0, 10
    syscall

space_full_message:
    li $v0, 4
    la $a0, prompt_space_full
    syscall
    j exit
