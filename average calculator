using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Newtonsoft.Json;
using System.Net.Http.Headers;

namespace AverageCalculatorApi.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class NumbersController : ControllerBase
    {
        private static readonly HttpClient httpClient = new HttpClient();
        private const int WindowSize = 10;
        private static List<int> window = new List<int>();

        [HttpGet("{numberid}")]
        public async Task<IActionResult> GetNumbers(string numberid)
        {
            if (!IsValidId(numberid))
                return BadRequest(new { error = "Invalid number ID" });

            var newNumbers = await FetchNumbersFromTestServer(numberid);
            if (newNumbers == null)
                return StatusCode(500, new { error = "Failed to fetch numbers" });

            var previousWindow = new List<int>(window);
            UpdateWindow(newNumbers);

            var response = new
            {
                numbers = newNumbers,
                windowPrevState = previousWindow,
                windowCurrState = window,
                avg = window.Any() ? window.Average() : 0
            };

            return Ok(response);
        }

        private static bool IsValidId(string id)
        {
            var validIds = new[] { "primes", "fibo", "even", "rand" };
            return validIds.Contains(id);
        }

        private static async Task<List<int>> FetchNumbersFromTestServer(string numberid)
        {
            try
            {
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJNYXBDbGFpbXMiOnsiZXhwIjoxNzE5ODEyNjc2LCJpYXQiOjE3MTk4MTIzNzYsImlzcyI6IkFmZm9yZG1lZCIsImp0aSI6IjI0NjhjMTVkLTA0NGQtNDcyNy1iZGVlLWEyYzM4M2RiZmViOSIsInN1YiI6ImFiaGlqZWV0a3VtYXI0NzI0NEBnbWFpbC5jb20ifSwiY29tcGFueU5hbWUiOiJNYWhhcmlzaGkgTWFya2FuZGVzaHdhciBEZWVtZWQgdG8gYmUgVW5pdmVyc2l0eSIsImNsaWVudElEIjoiMjQ2OGMxNWQtMDQ0ZC00NzI3LWJkZWUtYTJjMzgzZGJmZWI5IiwiY2xpZW50U2VjcmV0Ijoia05kZlFrWWRzem5Nd1laVyIsIm93bmVyTmFtZSI6IkFiaGlqZWV0Iiwib3duZXJFbWFpbCI6ImFiaGlqZWV0a3VtYXI0NzI0NEBnbWFpbC5jb20iLCJyb2xsTm8iOiIxMTIxMjY1MSJ9.rfyjUsBna6oltX2Yn54jaHnpxM0_BuEUWDyvfZ4h988","expires_in");
                var response = await httpClient.GetAsync($"http://20.244.56.144/test/{numberid}"); // Replace with the actual API URL
                response.EnsureSuccessStatusCode();
                var content = await response.Content.ReadAsStringAsync();
                var numbersResponse = JsonConvert.DeserializeObject<NumbersResponse>(content);
                return numbersResponse.Numbers;
            }
            catch
            {
                return null;
            }
        }

        private static void UpdateWindow(List<int> newNumbers)
        {
            foreach (var num in newNumbers)
            {
                if (!window.Contains(num))
                {
                    if (window.Count >= WindowSize)
                    {
                        window.RemoveAt(0);
                    }
                    window.Add(num);
                }
            }
        }

        private class NumbersResponse
        {
            public List<int> Numbers { get; set; }
        }
    }
}
