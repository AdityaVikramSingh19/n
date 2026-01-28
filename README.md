Webhooks & External Integrations

Q1 .File name OrderController.js

package org.example.evaluations.evaluation.controllers;
import java.util.UUID;

import org.example.evaluations.evaluation.dtos.OrderRequestDto;
import org.example.evaluations.evaluation.models.Order;
import org.example.evaluations.evaluation.services.IOrderService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/order")
public class OrderController {

    @Autowired
    private IOrderService orderService;

    //Please add APIs here
    @PostMapping()
    public Order CreateOrder(@RequestBody OrderRequestDto orderRequestDto){
        return orderService.createOrder(orderRequestDto.getCustomerId(),orderRequestDto.getTotalAmount());
    }
    @DeleteMapping("/{orderId}")
    public boolean DeleteOrder( @PathVariable UUID orderId){
        return orderService.deleteOrder(orderId);
    }
}












File name : OrderService.js

package org.example.evaluations.evaluation.services;
import org.example.evaluations.evaluation.models.Order;
import org.example.evaluations.evaluation.models.OrderStatus;
import org.example.evaluations.evaluation.repositories.OrderRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import java.util.Date;
import java.util.UUID;
@Service
public class OrderService implements IOrderService {

    @Autowired
    private OrderRepository orderRepository;

    @Override
    public Order createOrder(Long customerId, Double totalAmount) {
        //Add your implementation here
        Order order = new Order();
        order.setCustomerId(customerId);
        order.setTotalAmount(totalAmount);
        order.setStatus(OrderStatus.CREATED);
        order.setCreatedAt(new Date());
        order.setLastUpdatedAt(new Date());
        order.setId(UUID.randomUUID());
        orderRepository.save(order);
        return order;

    }

    @Override
    public Boolean deleteOrder(UUID orderId) {
      //Add your implementation here and return result rather than returning false
      return orderRepository.remove(orderId);
    }}








Q2.Filename : InstallController.js


package org.example.evaluations.evaluation.controllers;

import org.example.evaluations.evaluation.dtos.InstallRequestDto;
import org.example.evaluations.evaluation.dtos.InstallResponseDto;
import org.example.evaluations.evaluation.models.InstallStatus;
import org.example.evaluations.evaluation.services.IDeployService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/install")
public class InstallController {

    @Autowired
    private IDeployService deployService;

    //Implement your API here
    @PostMapping
    public InstallResponseDto I(@RequestBody InstallRequestDto installRequestDto){
        if(installRequestDto.getAppId()==null){
            throw new IllegalArgumentException("AppId is null");
        }
        InstallStatus installStatus =deployService.install(installRequestDto.getAppId());
        InstallResponseDto installResponseDto = new InstallResponseDto();
        installResponseDto.setStatus(installStatus);
        installResponseDto.setAppId(installRequestDto.getAppId());
        return  installResponseDto;
    }
}






Filename : UpdateController.js

package org.example.evaluations.evaluation.controllers;

import java.util.UUID;

import org.example.evaluations.evaluation.dtos.CheckUpdateResponseDto;
import org.example.evaluations.evaluation.dtos.UpdateResponseDto;
import org.example.evaluations.evaluation.models.CheckUpdateStatus;
import org.example.evaluations.evaluation.models.UpdateStatus;
import org.example.evaluations.evaluation.models.Version;
import org.example.evaluations.evaluation.services.IDeployService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/update")
public class UpdateController {

    @Autowired
    private IDeployService deployService;

    //Implement your API here
    @PostMapping
    public UpdateResponseDto update(@RequestBody UpdateResponseDto updateRequestDto){
        if(updateRequestDto.getAppId()==null||updateRequestDto.getVersion()==null){
            throw new IllegalArgumentException("Either AppId or version is missing");
        }
        UpdateStatus updateS = deployService.update(updateRequestDto.getAppId(), updateRequestDto.getVersion().getValue());
        UpdateResponseDto updateRes = new UpdateResponseDto();
        updateRes.setAppId(updateRequestDto.getAppId());
        updateRes.setStatus(updateS);
        updateRes.setVersion(updateRequestDto.getVersion());
        return updateRes;
    }

    @GetMapping("/{id}/installedVersion/{version}")
    public CheckUpdateResponseDto check(@PathVariable("id") UUID id,@PathVariable("version") String version){
        Version LV = deployService.getLatestVersion(id, version);
        CheckUpdateResponseDto checK = new CheckUpdateResponseDto();
        if(LV == null){
            checK.setAppId(id);
            checK.setStatus(CheckUpdateStatus.NO_UPDATE);
            checK.setVersion(null);
        }else{
            checK.setAppId(id);
            checK.setStatus(CheckUpdateStatus.UPDATE_AVAILABLE);
            checK.setVersion(LV);
        }
        return checK;
    }

}

































//Filename  : OrderController.js

package org.example.evaluations.evaluation.controllers;

import java.util.List;

import org.example.evaluations.evaluation.models.Order;
import org.example.evaluations.evaluation.services.IOrderService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.example.evaluations.evaluation.services.IOrderService;

@RestController
@RequestMapping
public class OrderController {

    @Autowired
    private IOrderService orderService;

    @GetMapping("/order/{orderId}")
    public Order getOrderByID(@PathVariable Long orderId){
        return orderService.getOrderById(orderId);
    } 
    @GetMapping("/order")
    public List<Order> GetAllOrders(){
        return orderService.getAllOrders();
    }

    //Please implement GET Apis with path `/order/{orderId}` and `/order` here
}










Q3 
FIle name: ContractualEmployeeController.js


package org.example.evaluations.evaluation.controllers;

import org.example.evaluations.evaluation.models.Permission;
import java.util.List;

import org.example.evaluations.evaluation.services.IEmployeeService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/contractualEmployee")
public class ContractualEmployeeController {

    @Autowired
    private IEmployeeService employeeService;

    /**
     * GET /contractualEmployee/isOnboarded/email/{email}?empId={id}
     */
    @GetMapping("/isOnboarded/email/{email}")
    public Boolean f1(
            @PathVariable String email,
            @RequestParam(required = false) Long empId
    ) {
        return employeeService.isOnboarded(empId,email);
    }

    /**
     * GET /contractualEmployee/permissions?roles=val1,val2
     * roles REQUIRED
     */
    @GetMapping(value = "/permissions", params = "roles")
    public List<Permission> getPermissions(
            @RequestParam List<String> roles
    ) {
        return employeeService.getPermissionsBasedOnRoles(roles);
    }

    /**
     * GET /contractualEmployee?email={email}&password={password}&name={name}
     * ALL optional
     */
    @GetMapping
    public Boolean validateEmployee(
            @RequestParam(required = false) String email,
            @RequestParam(required = false) String password,
            @RequestParam(required = false) String name
    ) {
        return employeeService.isIdentityProvided(email, password, name);
    }
}

































Q 1.

Filename  : OrderController.js



package org.example.evaluations.evaluation.controllers;


import org.example.evaluations.evaluation.services.IOrderService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.http.ResponseEntity;
import org.example.evaluations.evaluation.models.Order;
import java.util.List;
@RestController
@RequestMapping("/order")
public class OrderController {


   @Autowired
   private IOrderService orderService;


   //Please implement GET Apis with path `/order/{orderId}` and `/order` here
   @GetMapping("/{orderId}")
   public ResponseEntity<Order> getOrderById(@PathVariable Long orderId){
       Order order = orderService.getOrderById(orderId);
       if(order!=null){
           return ResponseEntity.ok(order);
       }
       return ResponseEntity.notFound().build();
   }
   @GetMapping()
   public ResponseEntity<List<Order>> getAllOrder(){
       List<Order> orders = orderService.getAllOrders();
       return ResponseEntity.ok(orders);
   }}
// OrderRepository
package org.example.evaluations.evaluation.repositories;


import org.example.evaluations.evaluation.models.Order;
import org.springframework.stereotype.Repository;


import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;


@Repository
public class OrderRepository {
   private Map<Long, Order> orders;


   public OrderRepository() {
       orders = new HashMap<>();
   }


   public Order findById(Long orderId) {
       //Please add logic for getting Order corresponding to particular
       // orderId from HashMap where orderId is stored as key and Order is stored
       //value in HashMap
        
       return orders.get(orderId);
   }


   public List<Order> findAll() {
       //Please add logic for getting Orders from HashMap
       //    List<Order> list = new ArrayList<>();
       //     for (Map.Entry<Long, Order> entry : orders.entrySet()) {
       //         list.add(entry.getValue());
       //     }
       //     return list;


       return new ArrayList<>(orders.values());
   }


   //Please don't delete below function
   public Order save(Order order) {
       orders.put(order.getId(),order);
       return orders.get(order.getId());
   }
}






































































// Q2


FILE NAME  : BookingController.js




package org.example.evaluations.evaluation.controllers;


import org.example.evaluations.evaluation.dtos.BookingUpdateRequestDto;
import org.example.evaluations.evaluation.models.Booking;
import org.springframework.web.bind.annotation.*;


import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;
import java.util.TimeZone;


@RestController
public class BookingController {


    @GetMapping("/booking/{bookingId:[\\d]+}")
    public Booking getBookingById(@PathVariable Long bookingId) {
        Booking booking = new Booking();
        booking.setId(bookingId);
        return booking;
    }




    @GetMapping("/booking/guest/{guest}/date/{date}")
    public Booking getBookingByGuestNameAndDate(@PathVariable("guest") String guestName,
                                                @PathVariable("date") String date) {
        Booking booking = new Booking();
        booking.setGuestName(guestName);
        Date Date = from(date);
        if(Date == null) return null;
        booking.setDate(Date);
        return booking;
    }




    @GetMapping("/booking/date/{date}")
    public List<Booking> listBookingsOfParticularDate(@PathVariable String date) {
        List<Booking> bookings = new ArrayList<>();
        Booking booking = new Booking();
        Date Date = from(date);
        if(Date == null) return null;
        booking.setDate(Date);
        bookings.add(booking);
        return bookings;
    }




    @PutMapping("/booking/{bookingId:[\\d]+}")
    public Booking updateBooking(@PathVariable Long bookingId, @RequestBody BookingUpdateRequestDto requestDto) {
        Booking booking = new Booking();
        booking.setId(bookingId);
        booking.setGuestName(requestDto.getGuestName());
        Date date = from(requestDto.getDate());
        if(date == null) return null;
        booking.setDate(date);
        return booking;
    }


    private Date from(String date) {
        try {
            SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
            simpleDateFormat.setTimeZone(TimeZone.getTimeZone("GMT"));
            return simpleDateFormat.parse(date);
        }catch (ParseException exception) {
            return null;
        }
    }
}

































































