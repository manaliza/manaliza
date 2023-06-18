using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerMovement : MonoBehaviour
{
    CharacterController controller;
    Animator anim;
    public float speed = 5;
    Transform cam;
    public float gravity = 10;
    float verticalvelocity = 0;
    public float jumbValue = 10;
    // Start is called before the first frame update
    void Start()
    {
        controller = GetComponent<CharacterController>();
        anim = GetComponentInChildren<Animator>();
        cam = Camera.main.transform;
    }

    // Update is called once per frame
    void Update()
    {
        float horizontal = Input.GetAxis("Horizontal");
        float vertical = Input.GetAxis("Vertical");
        bool isSprint = Input.GetKey(KeyCode.LeftShift);
        float sprint = isSprint ? 2.5f : 1;

        if(Input.GetMouseButtonDown(0))
        {
            anim.SetTrigger("Attack");
        }
        
        Vector3 movedirection = new Vector3 (horizontal, 0, vertical);
        anim.SetFloat("Speed", Mathf.Clamp(movedirection.magnitude,     0, 0.5f) + (isSprint ? 0.5f : 0));

        if (controller.isGrounded)
        {
            if (Input.GetAxis("Jump") > 0)
                verticalvelocity = jumbValue;
        }
        else
            verticalvelocity -= gravity * Time.deltaTime;
        
        if (movedirection.magnitude > 0.1f)
        {
            float angle = Mathf.Atan2(movedirection.x, movedirection.z) * Mathf.Rad2Deg + cam.eulerAngles.y;
            transform.rotation = Quaternion.Euler(0, angle, 0);
        }

        movedirection = cam.TransformDirection(movedirection);
        movedirection = new Vector3(movedirection.x * speed * sprint, verticalvelocity, movedirection.z * speed * sprint );
        controller.Move(movedirection * Time.deltaTime);
    }

    public void DoAttack()
    {
        transform.Find("collider").GetComponent<BoxCollider>().enabled = true;
        StartCoroutine(HideCollider());
    }

    IEnumerator HideCollider()
    {
        yield return new WaitForSeconds(0.5f);
        transform.Find("collider").GetComponent<BoxCollider>().enabled = false;
    }

}
