<template>
  <div class="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 p-6">
    <div class="max-w-6xl mx-auto">
      <!-- Header -->
      <div class="bg-white rounded-xl shadow-lg p-6 mb-6">
        <h1 class="text-3xl font-bold text-gray-800 mb-2">API Testing Tool</h1>
        <p class="text-gray-600">Test your APIs with custom headers, methods, and request bodies</p>
      </div>

      <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
        <!-- Request Panel -->
        <div class="bg-white rounded-xl shadow-lg overflow-hidden">
          <div class="bg-gradient-to-r from-blue-600 to-indigo-600 text-white p-4">
            <h2 class="text-xl font-semibold">Request Configuration</h2>
          </div>
          
          <div class="p-6 space-y-6">
            <!-- URL and Method -->
            <div class="space-y-4">
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">URL</label>
                <input 
                  v-model="url" 
                  class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent transition duration-200" 
                  placeholder="https://api.example.com/endpoint"
                  type="url"
                />
              </div>
              
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Method</label>
                <select 
                  v-model="method" 
                  class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent transition duration-200"
                >
                  <option v-for="m in methods" :key="m" :value="m">{{ m }}</option>
                </select>
              </div>
            </div>

            <!-- Tabs -->
            <div class="border-b border-gray-200">
              <nav class="-mb-px flex space-x-8">
                <button 
                  @click="activeTab = 'headers'"
                  :class="activeTab === 'headers' ? 'border-blue-500 text-blue-600' : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'"
                  class="py-2 px-1 border-b-2 font-medium text-sm transition duration-200"
                >
                  Headers
                </button>
                <button 
                  @click="activeTab = 'params'"
                  :class="activeTab === 'params' ? 'border-blue-500 text-blue-600' : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'"
                  class="py-2 px-1 border-b-2 font-medium text-sm transition duration-200"
                >
                  Params
                </button>
                <button 
                  v-if="showBody"
                  @click="activeTab = 'body'"
                  :class="activeTab === 'body' ? 'border-blue-500 text-blue-600' : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'"
                  class="py-2 px-1 border-b-2 font-medium text-sm transition duration-200"
                >
                  Body
                </button>
              </nav>
            </div>

            <!-- Headers Tab -->
            <div v-show="activeTab === 'headers'" class="space-y-4">
              <div class="flex justify-between items-center">
                <h3 class="text-lg font-medium text-gray-800">Headers</h3>
                <button 
                  @click="addHeader"
                  class="bg-green-500 hover:bg-green-600 text-white px-3 py-1 rounded-md text-sm transition duration-200"
                >
                  + Add Header
                </button>
              </div>
              
              <div class="space-y-3 max-h-64 overflow-y-auto">
                <div 
                  v-for="(header, index) in headers" 
                  :key="index"
                  class="flex items-center space-x-2 p-3 bg-gray-50 rounded-lg"
                >
                  <input 
                    type="checkbox" 
                    v-model="header.enabled"
                    class="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 rounded focus:ring-blue-500"
                  />
                  <input 
                    v-model="header.key" 
                    placeholder="Key"
                    class="flex-1 px-3 py-2 border border-gray-300 rounded focus:ring-1 focus:ring-blue-500 text-sm"
                  />
                  <input 
                    v-model="header.value" 
                    placeholder="Value"
                    class="flex-1 px-3 py-2 border border-gray-300 rounded focus:ring-1 focus:ring-blue-500 text-sm"
                  />
                  <button 
                    @click="removeHeader(index)"
                    class="text-red-500 hover:text-red-700 p-1 transition duration-200"
                  >
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
                    </svg>
                  </button>
                </div>
              </div>
            </div>

            <!-- Params Tab -->
            <div v-show="activeTab === 'params'" class="space-y-4">
              <div class="flex justify-between items-center">
                <h3 class="text-lg font-medium text-gray-800">Query Parameters</h3>
                <button 
                  @click="addParam"
                  class="bg-green-500 hover:bg-green-600 text-white px-3 py-1 rounded-md text-sm transition duration-200"
                >
                  + Add Param
                </button>
              </div>
              
              <div class="space-y-3 max-h-64 overflow-y-auto">
                <div 
                  v-for="(param, index) in params" 
                  :key="index"
                  class="flex items-center space-x-2 p-3 bg-gray-50 rounded-lg"
                >
                  <input 
                    type="checkbox" 
                    v-model="param.enabled"
                    class="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 rounded focus:ring-blue-500"
                  />
                  <input 
                    v-model="param.key" 
                    placeholder="Key"
                    class="flex-1 px-3 py-2 border border-gray-300 rounded focus:ring-1 focus:ring-blue-500 text-sm"
                  />
                  <input 
                    v-model="param.value" 
                    placeholder="Value"
                    class="flex-1 px-3 py-2 border border-gray-300 rounded focus:ring-1 focus:ring-blue-500 text-sm"
                  />
                  <button 
                    @click="removeParam(index)"
                    class="text-red-500 hover:text-red-700 p-1 transition duration-200"
                  >
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
                    </svg>
                  </button>
                </div>
              </div>

              <!-- URL Preview -->
              <div v-if="finalUrl !== url" class="mt-4">
                <label class="block text-sm font-medium text-gray-700 mb-2">Final URL:</label>
                <div class="bg-yellow-50 border border-yellow-200 rounded-lg p-3">
                  <code class="text-sm text-yellow-800 break-all">{{ finalUrl }}</code>
                </div>
              </div>
            </div>

            <!-- Body Tab -->
            <div v-show="activeTab === 'body' && showBody" class="space-y-4">
              <div class="flex justify-between items-center">
                <h3 class="text-lg font-medium text-gray-800">Request Body</h3>
                <div class="flex space-x-2">
                  <select 
                    v-model="bodyType" 
                    class="px-3 py-1 border border-gray-300 rounded text-sm focus:ring-1 focus:ring-blue-500"
                  >
                    <option value="json">JSON</option>
                    <option value="form">Form Data</option>
                    <option value="raw">Raw Text</option>
                  </select>
                  <button 
                    v-if="bodyType === 'json'"
                    @click="formatJson"
                    class="bg-indigo-500 hover:bg-indigo-600 text-white px-3 py-1 rounded-md text-sm transition duration-200"
                  >
                    Format JSON
                  </button>
                </div>
              </div>

              <!-- JSON Body -->
              <div v-if="bodyType === 'json'">
                <textarea 
                  v-model="body" 
                  rows="8"
                  class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent font-mono text-sm"
                  placeholder='{"key": "value"}'
                ></textarea>
              </div>

              <!-- Form Data Body -->
              <div v-else-if="bodyType === 'form'" class="space-y-4">
                <div class="flex justify-between items-center">
                  <span class="text-sm text-gray-600">Form Data Fields</span>
                  <button 
                    @click="addFormField"
                    class="bg-green-500 hover:bg-green-600 text-white px-3 py-1 rounded-md text-sm transition duration-200"
                  >
                    + Add Field
                  </button>
                </div>
                
                <div class="space-y-3 max-h-64 overflow-y-auto">
                  <div 
                    v-for="(field, index) in formFields" 
                    :key="index"
                    class="flex items-center space-x-2 p-3 bg-gray-50 rounded-lg"
                  >
                    <input 
                      type="checkbox" 
                      v-model="field.enabled"
                      class="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 rounded focus:ring-blue-500"
                    />
                    <input 
                      v-model="field.key" 
                      placeholder="Field Name"
                      class="flex-1 px-3 py-2 border border-gray-300 rounded focus:ring-1 focus:ring-blue-500 text-sm"
                    />
                    <input 
                      v-model="field.value" 
                      placeholder="Field Value"
                      class="flex-1 px-3 py-2 border border-gray-300 rounded focus:ring-1 focus:ring-blue-500 text-sm"
                    />
                    <button 
                      @click="removeFormField(index)"
                      class="text-red-500 hover:text-red-700 p-1 transition duration-200"
                    >
                      <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
                      </svg>
                    </button>
                  </div>
                </div>
              </div>

              <!-- Raw Text Body -->
              <div v-else-if="bodyType === 'raw'">
                <textarea 
                  v-model="rawBody" 
                  rows="8"
                  class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent font-mono text-sm"
                  placeholder="Raw text content..."
                ></textarea>
              </div>
            </div>

            <!-- Action Buttons -->
            <div class="flex space-x-3 pt-4">
              <button 
                @click="fetchData" 
                :disabled="loading"
                class="flex-1 bg-gradient-to-r from-blue-600 to-indigo-600 hover:from-blue-700 hover:to-indigo-700 disabled:from-gray-400 disabled:to-gray-400 text-white px-6 py-3 rounded-lg font-medium transition duration-200 flex items-center justify-center"
              >
                <svg v-if="loading" class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                  <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                  <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                </svg>
                {{ loading ? 'Sending...' : 'Send Request' }}
              </button>
              <button 
                @click="clearResult"
                class="bg-gray-500 hover:bg-gray-600 text-white px-6 py-3 rounded-lg font-medium transition duration-200"
              >
                Clear
              </button>
            </div>
          </div>
        </div>

        <!-- Response Panel -->
        <div class="bg-white rounded-xl shadow-lg overflow-hidden">
          <div class="bg-gradient-to-r from-green-600 to-emerald-600 text-white p-4 flex justify-between items-center">
            <h2 class="text-xl font-semibold">Response</h2>
            <button 
              v-if="result"
              @click="showModal = true"
              class="bg-white/20 hover:bg-white/30 text-white px-3 py-1 rounded-md text-sm transition duration-200 flex items-center space-x-2"
            >
              <span>Full Screen</span>
            </button>
          </div>
          
          <div class="p-6">
            <div class="bg-gray-900 text-gray-100 rounded-lg p-4 font-mono text-sm overflow-auto" style="min-height: 400px; max-height: 600px;">
              <pre v-if="result" class="whitespace-pre-wrap text-green-400">{{ result }}</pre>
              <div v-else class="text-gray-500 italic">
                Response will appear here...
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal Response -->
    <div v-if="showModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4 z-50">
      <div class="bg-white rounded-xl shadow-2xl max-w-6xl w-full max-h-[90vh] flex flex-col">
        <!-- Modal Header -->
        <div class="bg-gradient-to-r from-green-600 to-emerald-600 text-white p-4 rounded-t-xl flex justify-between items-center">
          <h3 class="text-xl font-semibold">Full Response</h3>
          <div class="flex items-center space-x-3">
            <button 
              @click="copyResponse"
              class="bg-white/20 hover:bg-white/30 text-white px-3 py-1 rounded-md text-sm transition duration-200 flex items-center space-x-2"
            >
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 16H6a2 2 0 01-2-2V6a2 2 0 012-2h8a2 2 0 012 2v2m-6 12h8a2 2 0 002-2v-8a2 2 0 00-2-2h-8a2 2 0 00-2 2v8a2 2 0 002 2z"></path>
              </svg>
              <span>Copy</span>
            </button>
            <button 
              @click="showModal = false"
              class="bg-white/20 hover:bg-white/30 text-white p-1 rounded-md transition duration-200"
            >
              <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
              </svg>
            </button>
          </div>
        </div>
        
        <!-- Modal Body -->
        <div class="flex-1 p-6 overflow-hidden">
          <div class="bg-gray-900 text-gray-100 rounded-lg p-6 font-mono text-sm h-full overflow-auto">
            <pre class="whitespace-pre-wrap text-green-400 leading-relaxed">{{ result }}</pre>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const url = ref('')
const method = ref('GET')
const headers = ref([
  { key: 'Accept', value: 'application/json', enabled: true },
  { key: 'Content-Type', value: 'application/json', enabled: true }
])
const params = ref([])
const body = ref(JSON.stringify({
  email: 'bangnaja300@gmail.com',
  password: '13377'
}, null, 2))
const rawBody = ref('')
const bodyType = ref('json')
const formFields = ref([
  { key: 'email', value: 'bangnaja300@gmail.com', enabled: true },
  { key: 'password', value: '13377', enabled: true }
])
const result = ref('')
const loading = ref(false)
const activeTab = ref('headers')
const showModal = ref(false)

const methods = ['GET', 'POST', 'PUT', 'DELETE', 'PATCH']

const showBody = computed(() => {
  return ['POST', 'PUT', 'PATCH'].includes(method.value)
})

const finalUrl = computed(() => {
  if (!url.value) return ''
  
  const enabledParams = params.value.filter(p => p.enabled && p.key.trim() && p.value.trim())
  if (enabledParams.length === 0) return url.value
  
  const urlObj = new URL(url.value)
  enabledParams.forEach(param => {
    urlObj.searchParams.set(param.key.trim(), param.value.trim())
  })
  
  return urlObj.toString()
})

const addHeader = () => {
  headers.value.push({ key: '', value: '', enabled: true })
}

const removeHeader = (index) => {
  headers.value.splice(index, 1)
}

const addParam = () => {
  params.value.push({ key: '', value: '', enabled: true })
}

const removeParam = (index) => {
  params.value.splice(index, 1)
}

const addFormField = () => {
  formFields.value.push({ key: '', value: '', enabled: true })
}

const removeFormField = (index) => {
  formFields.value.splice(index, 1)
}

const fetchData = async () => {
  if (!url.value.trim()) {
    result.value = 'ERROR: URL is required'
    return
  }

  loading.value = true
  
  try {
    const requestHeaders = {}
    headers.value
      .filter(h => h.enabled && h.key.trim() && h.value.trim())
      .forEach(h => {
        requestHeaders[h.key.trim()] = h.value.trim()
      })

    const requestOptions = {
      method: method.value,
      headers: requestHeaders
    }

    if (showBody.value) {
      if (bodyType.value === 'json' && body.value.trim()) {
        try {
          JSON.parse(body.value) // Validate JSON
          requestOptions.body = body.value
        } catch (e) {
          result.value = 'ERROR: Invalid JSON in request body'
          loading.value = false
          return
        }
      } else if (bodyType.value === 'form') {
        const formData = new FormData()
        formFields.value
          .filter(f => f.enabled && f.key.trim())
          .forEach(f => {
            formData.append(f.key.trim(), f.value || '')
          })
        requestOptions.body = formData
        // Remove Content-Type header for FormData (browser will set it automatically with boundary)
        delete requestOptions.headers['Content-Type']
      } else if (bodyType.value === 'raw' && rawBody.value.trim()) {
        requestOptions.body = rawBody.value
      }
    }

    const startTime = Date.now()
    const res = await fetch(finalUrl.value, requestOptions)
    const endTime = Date.now()
    
    const responseData = await res.text()
    let parsedData
    
    try {
      parsedData = JSON.parse(responseData)
    } catch (e) {
      parsedData = responseData
    }

    const responseInfo = {
      status: res.status,
      statusText: res.statusText,
      responseTime: `${endTime - startTime}ms`,
      headers: Object.fromEntries(res.headers.entries()),
      data: parsedData
    }

    result.value = JSON.stringify(responseInfo, null, 2)
  } catch (err) {
    result.value = 'ERROR:\n' + err.message
    console.error(err)
  } finally {
    loading.value = false
  }
}

const clearResult = () => {
  result.value = ''
}

const formatJson = () => {
  if (body.value.trim()) {
    try {
      const parsed = JSON.parse(body.value)
      body.value = JSON.stringify(parsed, null, 2)
    } catch (e) {
      // Invalid JSON, do nothing
    }
  }
}

const copyResponse = async () => {
  try {
    await navigator.clipboard.writeText(result.value)
    // You can add a toast notification here if needed
    console.log('Response copied to clipboard!')
  } catch (err) {
    console.error('Failed to copy response:', err)
  }
}
</script>